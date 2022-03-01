# Device model

## Blocks

The control model defines a class named **ncaBlock** that is a container for objects. Subclasses of **ncaBlock** are called **block classes**. Each block class defines a specific configuration of objects.

In Devices, block classes are instantiated to make objects known as **blocks.** A block class may be instantiated as many times as required to define the required control API. For example, an eight-channel audio mixer might define a block class named **myMixChannel** and instantiate it eight times, to create eight blocks which define the eight required channel-control APIs.

### Nested blocks

A block may contain other blocks. The contained blocks are said to be **nested** inside the containing block. For example, the **myMixChannel** class might specify an instance of a block named **EqA** that defines an equalizer section. **EqA** is "nested" inside **myMixChannel**.

### Block namespaces

Every NCA object has a name that is unique _within its containing block_ (see [Identification](Identification.md), and every block constitutes a namespace for the objects it contains.  This name is known as the _**role name**_.

If a block class is instantiated multiple times in a device, the role names of its contained objects (e.g. `Gain`, `Mute`, `Pad`,...) will not conflict, because each of the eight block objects is a separate namespace.

Every NCA object is uniquely identified by its _**role path**_.  See [**Identification(Role path)**](Identification.md#RolePath).

<a name="Blockspecs"/> ## Blockspecs and Blockspec IDs ##

In NCA, a **schema** is a YANG code block (text file) that defines a block class. Schemas are built according to the following rules:

- A schema defines a block class as an RFC 6095 YANG complex type.
- The block class must be defined as an extension of **ncaBlock** or an extension of a subclass of **ncaBlock**.
- The schema must import the schemas of all nested blocks - see [Schema composition](#schema-composition).
- The schema must contain instance declarations of all the block's objects and all its nested blocks, as needed to define the block's control API.
- The schema may specify the block class's signal flow, if signal paths are being used - see [Signal paths](#signal-paths).
- Every schema is identified by a **schema ID.**
- For schemas which are intended to be used across industry or across products, this **schema ID** value will be constructed in a way that is globally unique - a Global Schema ID (GSID) value - see [Identification](Identification.md)).

A schema is not a block, it is only the description of a block. Schemas are the standardized way of documenting the contents of block classes within NCA.

NCA also allows blocks whose contents are dynamic at runtime, see [Device configurability](#device-configurability).

A block class may be **instantiated** any number of times within a device.

Schema concepts are illustrated in `Figure 2` which shows both the Control model and the Device model.

| ![The ncaBlock abstract class, a block class, two blocks, and related YANG documents](images/Figure-2.png) |
|:--:|
| *Figure 2. The ncaBlock abstract class, a block class, two blocks, and related YANG documents* |

1. On the left is the control model with its YANG definition files. In this case, only two control model classes are shown: **ncaRoot** , which is the parent of everything, and **ncaBlock** , which is the abstract base class upon which all block classes are built.
2. On the right is an example Device model showing a block class named **ncaBlockA** and its instances. In this example, **ncaBlockA** is instantiated twice, once as block **X** and once as block **Y**.

`Figure 2` illustrates that NCA uses YANG for two distinct purposes:

1. Defining the control model's class tree.
2. Defining schemas.

These purposes are separate, and involve two separate sets of files. It is expected that in the long run, the control model files (case 1) will become relatively static, and generally subject to common specification by a governing authority; on the other hand, the repertoire of schema files (case 2) will continue to grow and evolve for an indefinite period, many of them being product-specific or vendor-specific.

_Note: YANG documentation uses the word "schema" more generally than NCA does. In YANG terminology, a "schema" is_ **any** _YANG definition file; in NCA language, a "schema" is a YANG definition of a specific block class's contents._

### Schema composition

When a block contains nested blocks, its schema imports the nested blocks' schemas and defines instances of them.

The design process of creating a schema by including one or more other schemas is called **schema composition**, the resulting schema is called a **composite schema** **, a** nd the corresponding block is called a **composite block.** By retrieving the value of the **schemaID** property for each of a composite block's nested blocks - recursing to inner nesting levels as necessary - a Controller can acquire a complete definition of the block's capabilities.

In addition to the schemaID, block classes include methods for retrieving the inventory of contained objects, as further detailed in section (insert reference link to 11.6 here)

Creating a composite schema has three main steps:

1. Importing the nested schemas using YANG **import** statements.
2. Defining instances of the nested schemas in the composite schema using YANG **ct:instance** statements.
3. Adding whatever other objects are needed in the composite schema using standard YANG methods.

### Device schemas

A **Device schema** is the schema that describes the complete control API of a (real or virtual) Device. It includes:

- The list of the Device's housekeeping objects
- The root block schema (which includes the schemas of blocks contained within the root block).

#### Base Device Schema

NCA defines the **Base Device Schema** **,** which is a Device schema that all Device schemas must implement to be NCA-compliant. It defines a minimal list of housekeeping objects and an empty root block.

### Schema reuse

A prime goal of NCA is to support a growing ecosystem of schemas that can be reused by Device developers. NCA (as an architecture) does not define this ecosystem or its management, but it does provide a few features to support it.

It is expected that reusable schemas will be maintained in public, corporate, and personal libraries, from which they can be retrieved for various purposes. Such purposes will include:

- Software development of Controllers and Devices;
- In-situ diagnosis of network media system problems;
- Setup of generic, self-configuring Controllers; and
- Training and tutorial processes.

It is further expected that these reusable schemas will mainly be component schemas. AMWA's process for developing, publishing, maintaining, and curating these schemas is beyond the scope of this document.

Since schemas are YANG documents, the envisioned reusable schema ecosystem will be fundamentally concerned with the storage, retrieval, and maintenance of YANG texts.

### Schema identification

A key NCA feature to support schema libraries is the Global Schema ID, or GSID. In schema libraries, a unique GSID value will be assigned to each schema intended for reuse. GSIDs will be the principal query key for people or systems seeking to retrieve schema definitions from public or corporate libraries, and will be the primary way Controllers retrieve definitions of schemas that Devices implement.

The syntax of the GSID is defined in [Identification](Identification.md).

### Schemas and APIs

A block's complete API is equal to the union of the APIs of its members, including members of other nested blocks, if any. Therefore, the block's schema constitutes a definition of the block's entire API.

The API the schema defines is a protocol-independent construct, expressed in terms of objects and their properties, methods, and events. Controllers will access it using whatever NCA protocol(s) the Device implements.

## Block enumeration mechanisms

The base ncaBlock class defines enumeration functions that allow Controllers to interrogate blocks and their sub-blocks directly, to reveal their detailed contents.

The block enumeration functions are regular methods of **ncaBlock-** derived classes, accessed in the usual manner through protocol exchanges between Controller and Device. In the case of dynamically-defined devices in which the contents of blocks may vary at runtime, the property-change event can be used to notify controllers about changes to the ncaBlock inventory.

## Signal paths

A block may expose a set of **signal paths**. A signal path is the NCA abstraction that represents a signal flow from one endpoint to another inside a block.

Signal path endpoints are called **NCA ports** or, in context, just **ports**. One or more ports can belong to any object with media signal functionality - processing, monitoring, storing, generating, sending, receiving, etcetera.

**NcaBlocks** can have ports. A port belonging to a block is called a **block port.** A block port is special in that it can connect to both ports of objects outside the block and ports of objects inside the block. Thus, signals enter and leave blocks via block ports.

`Figure 3` shows the ports and signal paths of a simple multistage audio equalizer block.

| ![A simple block, its block ports, its members and their ports, and its signal paths](images/Figure-3.png) |
|:--:|
| *Figure 3. A simple block, its block ports, its members and their ports, and its signal paths* |

Signal paths cannot cross block boundaries - no signal path may extend from a port inside a given block to a port outside that same block. However, a signal path may extend from a port inside a given block to a block port of that same block, and from there to other block ports, etcetera.

Ports that send signals are called **output ports**; ports that receive signals are called **input ports**. An output port may connect to multiple downstream input ports, but an input port may connect to at most one upstream output port.

The complete set of signal paths for a block is called the block's **signal flow**.

Use of the signal path mechanism is preferred, but not strictly required.

Depending on implementation, a block's signal flow may be read-write or read-only. In a fully-dynamic block, Controllers can retrieve and modify the signal flow. In a static block, Controllers can retrieve the signal flow but not modify it. The **ncaBlock** class has methods for retrieving and, if supported, modifying block signal paths.

## Complete block example

A complete block example is shown in `Figure 4`. This example illustrates both block composition and signal flow. The Device shown is a simplified microphone preamplifier. It includes a nested instance of the equalizer block shown in `Figure 3`.

| ![Example microphone preamplifier](images/Figure-4.png) |
|:--:|
| *Figure 4. Example microphone preamplifier* |

## Device structure

As illustrated in `Figure 5` below, the Device model envisions a Device as a **root block** accompanied by a few housekeeping objects. The root block is an instance of the **ncaBlock** subclass **ncaRootBlock****.** The root block contains all the objects that control the device's application functions. In simple devices, these objects might be contained directly in the root block; in more complex devices, they will be contained in a hierarchy of blocks nested inside the root block.

Every Device contains exactly one instance of **ncaRootBlock**.

An object contained in a block is called a **member** or **member object** of that block.

A Device may have certain application functions the designers wish not to be network-controllable. Such functions will not be represented in the root block or its nested blocks, and will consequently not be in the device's control API.

The housekeeping objects are called **managers** and are discussed in [Managers](Managers.md).

| ![NCA device](images/Figure-5.png) |
|:--:|
| *Figure 5. NCA device* |

## Device configurability

"Device configurability" means the degree to which an NCA Device's object population and internal signal paths can be defined at run time, as opposed to time of manufacture.

As shown in `Table 1`, NCA identifies four levels of Device configurability. The levels are further classified into **static** and **dynamic** categories. A Controller of a static Device can assume its configuration is fixed. A Controller of a dynamic Device can make no such assumption. In environments with multiple Controllers, each Controller will need to monitor the configurations of dynamic Devices to track changes.

| **Configurability** | **Category** | **Description** |
| --- | --- | --- |
| Fixed | static | The Device has a permanently assigned object repertoire and signal-flow topology, defined at time of firmware programming. |
| Pluggable | static | The object repertoire and signal flow of the Device may be changed while the Device is offline, by plugging and unplugging of hardware modules, adjustment of physical controls, reloading or readjustment of software, or other manual means. |
| Partially configurable | dynamic | Controllers may change the signal-flow topology of the Device while online. |
| Fully configurable | dynamic | A superset of 'partially-configurable', with the addition that Controllers may create and delete objects inside the Device while online. |

*Table 1. Device configurability*
