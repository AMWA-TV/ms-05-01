# Identification

NCA defines the following framework identifiers (a "framework" identifier is an identifier that is used by the basic NCA framework, irrespective of application).

## Identifiers

### Class ID

Uniquely identifies a class.  Stored in property `NcObject.classId`.  Every class shall inherit this property, and this property shall have a valid value for every class and every class instance.

| Datatype     | Scope             | Lifespan  | Reference |
| -----------  | ----------------- | ----------| ------------------------------------------------------- |
| `NcclassId`  | NCA control model | Permanent |[Appendix A](Appendix%20A%20-%20Class%20ID%20Format.md)  |
|              |                   |           |                                                         |

### Class version

Uniquely identifies the version of a class.  Stored in property `NcObject.classVersion`.  Every class shall inherit this property, and this property shall have a valid value for every class and every class instance.  The value shall conform to the Semantic Versioning 2.0.0 convention, defined [here](https://semver.org/).

| Datatype        | Scope             | Lifespan  | Reference |
| --------------  | ----------------- | ----------| ------------------------------------------------------- |
| `NcVersionCode` | NCA control model | Permanent |[Appendix A](Appendix%20A%20-%20Class%20ID%20Format.md)  |
|                 |                   |           |                                                         |

### Object ID

Compact read-only handle that uniquely identifies an object within its device. Stored in property `NcObject.oid`.  Every class shall inherit this property, and this property shall have a valid value for every object.

| Datatype     | Scope    | Lifespan  | Reference |
| ------------ | -------- | --------- | ---------------------------------------------------------------- |
| `NcOid`      | Device   |           | [Object IDs](#object-ids), below                                 |
|              |          |           |                                                                  |

### Element ID

Static identifier of a property, method, or event of a control class. Values shall be defined by the class specification in the control model, and shall not change over time. Values shall be unique within each class.

| Datatype     | Scope    | Lifespan  | Reference |
| ------------ | -------- | --------  | ---------------------------------------------------------------- |
| `NcElementId`| Class    | Permanent | [Appendix B](Appendix%20B%20-%20Property%2C%20Method%2C%20Event%20IDs.md)|
|              |          |           |                                                                  |

### Role

Static, read-only text property that names an object within the block to which it belongs. Unique within the block. Stored in property `NcObject.role`. Every class shall inherit this property, and this property shall have a valid value for every object.

`Role` values are analogous to labels on a physical control panel, e.g.
**Input Gain**, **Output Select**, etcetera., or to filenames in a hierarchical file system.

| Datatype     | Scope    | Lifespan  | Reference |
| ----------   | -------- | --------  | ---------------------------------------------------------------- |
| `NcString`   | Block    | Permanent |                                                                  |
|              |          |           |                                                                  |

### Role path

Unique identifier of an object within a device.  Sequence of strings, consisting of the `role` values of all the object's containing blocks, starting from the root block and ending with the object's own `role` value.
In some implementations, object IDs may change over time, but role paths shall not, unless the device is dynamically reconfigurable.

| Datatype     | Scope    | Lifespan  | Reference |
| ------------ | -------- | --------  | ---------------------------------------------------------------- |
| `NcRolePath` | Device   | Permanent |                                                                  |
|              |          |           |                                                                  |

### User label

Read/write string that users may set to identify an object's operational purpose in a particular application context. Analogous to the texts written on mixing console scribble strips, labels, and bits of paper tape affixed to equipment in use. "Elvis's Vocal Mic", "First violins #3", etcetera.  Stored in property `NcObject.userLabel`. Every class shall inherit this property, so any object may have a user label value.  There are no constraints on the syntax or lifetime of user labels - they are intended to convey application information of any and all kinds.

| Datatype     | Scope    | Lifespan  | Reference |
| ------------ | -------- | --------  | ---------------------------------------------------------------- |
| `NcString`   | n/a      | volatile  |                                                                  |
|              |          |           |                                                                  |

### Product UUID

UUID that shall uniquely identify the particular product that constitutes the Device. Stored in property `uuid` of datatype `NcProduct`.  In turn, the value of `NcProduct` for the Device shall be stored in property `NcDeviceManager.product`.

| Datatype        | Scope    | Lifespan  | Reference |
| --------------- | -------- | --------  | ---------------------------------------------------------------- |
| `NcString`| Universe | Permanent |                                                                  |
|                 |          |           |                                                                  |

### Blockspec ID

Identifier of a blockspec.  Stored in property `NcBlock.specID`.

| Datatype     | Scope    | Lifespan  | Reference |
| ------------ | -------- | --------  | ---------------------------------------------------------------- |
| `NcString`   | Varies   | Permanent |[Device Model (Blockspecs and Blockspec IDs)](Device%20Model.md#Blockspecs-and-Blockspec-IDs)|
|              |          |           |                                                                  |

## Object IDs

Object ID values are 32-bit unsigned integers.  No semantics are attached to particular values, and device designs may use any value greater than 4095.  Values 1...4095 shall be reserved for possible future NCA features. The value zero shall be reserved to denote the absence of an object.  

In some Devices, object IDs can change from time to time.  Each block object shall maintain an object ID revision timestamp that indicates the last time the object IDs of its members block have changed.  As well, the readonly boolean property `NcObject.constantOid`, inherited by every class and instantiated in every object, shall be TRUE when the object's OID is fixed at time of manufacture and does not change thereafter, FALSE otherwise.

## NCA-NMOS identity mapping

NCA's **Touchpoint** feature allows applications to attach a list of _touchpoints_ to any NCA object. A touchpoint is a defined relation between an entity in NCA and an entity in a different namespace.

This document defines the mapping to NMOS IS-xx resources; other mappings may be defined in the future, through specifications or by implementers as necessary.

The Touchpoint feature is implemented by the **Touchpoints** property of any **NcObject**. **Touchpoints** shall be a list of zero or more **touchpoint descriptors**. For a given **NcObject**, each touchpoint descriptor shall identify a foreign namespace and a resource identifier within the foreign namespace.

Because **Touchpoints** is a property of **NcObject**, it shall be inherited by every NCA object and may be used wherever required.

## NMOS Namespaces

Namespaces for NMOS are as follows:

- IS-04: **x-nmos** (includes the identities which link to IS-05 and IS-07)
- IS-08: **x-nmos/channelmapping** (special touchpoints for IS-08 input and output resources)

### Touchpoint Resource for NMOS

The NMOS touchpoint resource shall include:

- Resource type type of resource linked
- ID identity of the resource in the given namespace

For the **x-nmos** namespace, **NcTouchpointResourceNmos** is defined and shall support the following resource types:

- node
- device
- source
- flow
- sender
- receiver

For the **x-nmos/channelmapping** namespace, **NcTouchpointResourceNmos_is_08** is defined, and shall support the following resource types, and the touchpoint resource id should be that of the underlying NMOS device.

- input
- output

WebIDL models for the touchpoints are defined in the control model in [MS-05-02 NMOS Control Framework](https://specs.amwa.tv/ms-05-02).

## NCA discoverability in IS-04

Compliant NCA protocols will need to be advertised through existing IS-04 practices inside the controls array of an NMOS Device. The requirements are detailed in [IS-12 NMOS Control Protocol](https://specs.amwa.tv/is-12).
