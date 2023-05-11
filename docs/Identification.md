# Identification

NCA defines the following framework identifiers (a "framework" identifier is an identifier that is used by the basic NCA framework, irrespective of application).

## Identifiers

### Class ID

Uniquely identifies a class. Stored in property [NcObject.classId](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncobject)

| Datatype                                                                                     | Scope             | Lifespan  |
| -------------------------------------------------------------------------------------------  | ----------------- | ----------|
| [NcClassId](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncclassid)  | NCA control model | Permanent |
|                                                                                              |                   |           |

Further information provided in [Appendix A](Appendix%20A%20-%20Class%20ID%20Format.md).

### Object ID

Compact read-only handle that uniquely identifies an object within its device. Stored in property [NcObject.oid](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncobject).

| Datatype                                                                            | Scope    | Lifespan  |
| ----------------------------------------------------------------------------------- | -------- | --------- |
| [NcOid](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncoid) | Device   |           |
|                                                                                     |          |           |

### Element ID

Static identifier of a property, method, or event of a [control class](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#control-classes).

| Datatype                                                                                        | Scope    | Lifespan  |
| ----------------------------------------------------------------------------------------------- | -------- | --------  |
| [NcElementId](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncelementid) | Class    | Permanent |
|                                                                                                 |          |           |

### Role

Static, read-only text property that names an object within the block to which it belongs. Unique within the block. Stored in property [NcObject.role](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncobject).

| Datatype                                                                                    | Scope    | Lifespan  |
| ------------------------------------------------------------------------------------------- | -------- | --------  |
| [NcString](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#primitives) | Block    | Permanent |
|                                                                                             |          |           |

### Role path

Unique identifier of an object within a device model. Sequence of strings, consisting of the `role` values of all the object's containing blocks, starting from the root block and ending with the object's own `role` value.  
Role paths are enabled by the existence of [blocks](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncblock) in the device model.

| Datatype                                                                                      | Scope    | Lifespan  |
| --------------------------------------------------------------------------------------------- | -------- | --------  |
| [NcRolePath](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncrolepath) | Device   | Permanent |
|                                                                                               |          |           |

### User label

Read/write string that users may set to identify an object's operational purpose in a particular application context. Analogous to the texts written on mixing console scribble strips, labels, and bits of paper tape affixed to equipment in use. Stored in property [NcObject.userLabel](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncobject).

| Datatype                                                                                    | Scope    | Lifespan  |
| ------------------------------------------------------------------------------------------- | -------- | --------- |
| [NcString](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#primitives) | n/a      | Permanent |
|                                                                                             |          |           |

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

## NCA-NMOS identity mapping

NCA's **Touchpoint** feature allows applications to attach a list of _touchpoints_ to any NCA object. A touchpoint is a defined relation between an entity in NCA and an entity in a different namespace.

This document defines the mapping to NMOS IS-xx resources; other mappings may be defined in the future, through specifications or by implementers as necessary.

The Touchpoint feature is implemented by the **Touchpoints** property of any **NcObject**. **Touchpoints** shall be a list of zero or more **touchpoint descriptors**. For a given **NcObject**, each touchpoint descriptor shall identify a foreign namespace and a resource identifier within the foreign namespace.

Because **Touchpoints** is a property of **NcObject**, it shall be inherited by every NCA object and may be used wherever required.

## NMOS Namespaces

Namespaces for NMOS are as follows:

- IS-04: **x-nmos** (includes the identities which link to IS-05 and IS-07)
- IS-08: **x-nmos/channelmapping** (special touchpoints for IS-08 Audio Channel Mapping input and output resources)

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

For the **x-nmos/channelmapping** namespace, **NcTouchpointResourceNmosChannelMapping** is defined, and shall support the following resource types, and the touchpoint resource id should be that of the underlying NMOS device.

- input
- output

WebIDL models for the touchpoints are defined in the control model in [MS-05-02 NMOS Control Framework](https://specs.amwa.tv/ms-05-02).

## NCA discoverability in IS-04

Compliant NCA protocols will need to be advertised through existing IS-04 practices inside the controls array of an NMOS Device. The requirements are detailed in [IS-12 NMOS Control Protocol](https://specs.amwa.tv/is-12).
