# Device model

An _**NCA Device**_ is a physical or virtual device that publishes an NCA-conformant API. This API is organized around the concept of _**Blocks**_, which are containers for objects. The control model defines a class named `NcBlock` that is instantiated to create blocks.

A block may contain other blocks. A contained block is said to be **nested** inside its containing block.

## Device structure

As illustrated in the figure `NCA Device` below, the Device model envisions a Device as a [root block](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Blocks.html) accompanied by a few control objects. In simple devices, these objects might be contained directly in the root block; in more complex devices, they will be contained in a hierarchy of blocks nested inside the root block.

An object contained in a block is called a _**member**_ or _**member object**_ of that block.

A Device may have certain application functions the designers wish not to be network-controllable. Such functions will not be represented in the root block or its nested blocks, and will consequently not be in the device's control API.

| ![NCA device](images/Device.png) |
|:--:|
| _**NCA device**_ |

## Block namespaces

Every NCA object shall have a _role name_ that is unique within its containing block (see [Identification](Identification.md)) and every block shall constitute a namespace for the objects it contains.

An object's name in its containing block is known as its _**role name**_.

Every NCA object is uniquely identified by its _**role path**_. See [**Identification (Role path)**](Identification.md).

## Blockspecs and Blockspec IDs

A _**blockspec**_ is a JSON object that defines the structure of a block. The blockspec format is defined normatively in [MS-05-03 NMOS Control Block Specifications](https://specs.amwa.tv/ms-05-03). In general, a blockspec must adhere to the following rules:

A blockspec is not a block, it is only the description of a block. Blockspecs are the standardized way of documenting the contents of NCA blocks.

Within a device, a blockspec may be instantiated any number of times. For example, one might create a blockspec that defines an audio mixer channel, then instantiate it 64 times to define a 64-channel mixer.

Blockspec relationships are illustrated in the figure **Blockspec** below.

| ![The NcBlock abstract class, a block class, two blocks, and related documents](images/Blockspec.png) |
|:--:|
| **Blockspec**:  The **NcBlock** abstract class, a block class, two blocks, and related documents |

On the left of the figure is the control model with its WebIDL definition files. In this case, only two control model classes are shown: `NcObject`, which is the parent of everything, and `NcBlock`, which is the abstract base class upon which all block classes are built.

On the right is an example Device model showing blocks **X** and **Y**, the two blockspecs that define them, and the standard JSON schema for all blockspecs.

### Blockspec composition

When a block contains nested blocks, its blockspec imports the nested blocks' blockspecs and defines instances of them.

The design process of creating a blockspec by including one or more other blockspecs is called _**blockspec composition**_, the resulting blockspec is called a _**composite blockspec,**_ and the corresponding block is called a _**composite block**_.

By retrieving the value of the `specID` property for each of a composite block's nested blocks - recursing to inner nesting levels as necessary - a Controller can acquire a complete definition of the block's capabilities.

In addition to storing the blockspec ID, `NcBlock` defines methods for retrieving the detailed inventory of contained objects, as further detailed in section [Block enumeration](#block-enumeration).

### Blockspecs and APIs

A block's complete API is equal to the union of the APIs of its members, including members of other nested blocks, if any. Therefore, the block's blockspec constitutes a definition of its API.

The API the blockspec defines is a protocol-independent construct, expressed in terms of objects and their properties, methods, and events. Controllers can access it using an NCA compliant protocol that the Device implements.

#### Base Device blockspec

NCA defines the _**Base Device blockspec,**_ which is a root block blockspec that all Device blockspecs must implement to be NCA-compliant. It defines a minimal list of housekeeping objects, but no application function objects. The formal specification is in [MS-05-03 NMOS Control Block Specifications](https://specs.amwa.tv/ms-05-03).

### Blockspec libraries

A prime goal of NCA is to support a growing ecosystem of blockspecs that can be reused by Device developers. NCA (as an architecture) does not define this ecosystem or its management, but it does provide a few features to support it.

It is expected that reusable blockspecs will be maintained in public, corporate, and personal libraries, from which they can be retrieved for various purposes. Such purposes will include:

- Software development of Controllers and Devices;
- In-situ diagnosis of network media system problems;
- Setup of generic, self-configuring Controllers; and
- Training and tutorial processes.

Since blockspecs are JSON documents, the envisioned reusable blockspec ecosystem will be fundamentally concerned with the storage, retrieval, and maintenance of JSON texts.

### Global blockspec IDs

A key NCA feature to support blockspec libraries is the **Global blockspec ID**, or **GSID**. In blockspec libraries, a unique GSID value will be assigned to each blockspec intended for reuse. GSIDs will be the principal query key for people or systems seeking to retrieve blockspec definitions from public or corporate libraries, and will be the primary way Controllers retrieve definitions of blockspecs that Devices implement.

The syntax of the GSID is defined in [Identification](Identification.md).

## Block enumeration

The base **`NcBlock`** class defines enumeration functions that allow Controllers to interrogate blocks and their sub-blocks directly, to reveal their detailed contents.

The block enumeration functions are regular methods of **`NcBlock`**, accessed in the usual manner through protocol exchanges between Controller and Device. In the case of dynamically-defined devices in which the contents of blocks may vary at runtime, the property-change event can be used to notify controllers about changes to the NcBlock inventory.

## Signal paths

A block may expose a set of **signal paths**. A signal path is the NCA abstraction that represents a signal flow from one endpoint to another inside a block.

Signal path endpoints are called **NCA ports** or, in context, just **ports**. One or more ports can belong to any object with media signal functionality - processing, monitoring, storing, generating, sending, receiving, etcetera.

Blocks can have ports. A port belonging to a block is called a **block port.** A block port is special in that it can connect to both ports of objects outside the block and ports of objects inside the block. Thus, signals enter and leave blocks via block ports.

The figure **Equalizer** shows the ports and signal paths of a simple multistage audio equalizer block.

| ![A simple block, its block ports, its members and their ports, and its signal paths](images/Equalizer.png) |
|:--:|
| _**Equalizer**: A simple block, its block ports, its members and their ports, and its signal paths_|

Signal paths cannot cross block boundaries - no signal path may extend from a port inside a given block to a port outside that same block. However, a signal path may extend from a port inside a given block to a block port of that same block, and from there to other block ports, etcetera.

Ports that send signals are called **output ports**; ports that receive signals are called **input ports**. An output port may connect to multiple downstream input ports, but an input port may connect to at most one upstream output port.

The complete set of signal paths for a block is called the block's **signal flow**.

Use of the signal path mechanism is preferred, but not strictly required.

Depending on implementation, a block's signal flow may be read-write or read-only. In a fully-dynamic block, Controllers can retrieve and modify the signal flow. In a static block, Controllers can retrieve the signal flow but not modify it. The `NcBlock` class has methods for retrieving and, if supported, modifying block signal paths.

## Complete block example

A complete block example is shown in the figure **MicPre** below. This example illustrates both block composition and signal flow. The Device shown is a simplified microphone preamplifier. It includes a nested instance of the equalizer block shown in the figure **Equalizer** above.

| ![Example microphone preamplifier](images/MicPre.png) |
|:--:|
| _**MicPre**: Example microphone preamplifier_ |
