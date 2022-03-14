# Components of the architecture

## Models

NCA is based on two interrelated models:

- The **Framework**, which defines the repertoire of  NCA control and monitoring functions and the common mechanisms that they use. The Framework has two components:

	- **Control model:** An inheritance tree of control classes that specify NCA's control and monitoring repertoire. These classes are summarized in [Control Model](Control%20Model.md) and defined normatively in WebIDL interface definition language files in the `NC-Framework` repository.

	- **Core Mechanisms:**  Common mechanisms used by the control classes.  These are defined in [Core Mechanisms](Core%20Mechanisms.md).

- The **Device model**, which describes how control and management model elements are instantiated and configured to create a Device's network control API. The Device model is described in the [Device Model chapter of MS-05-01](Device%20Model.md).

As detailed in [Device Model](Device%20Model.md), definitions of particular Device models and Device model elements are contained in JSON files known as _**blockspecs**_. The JSON schemas defining the blockspec format are available in the `NC-Blockspecs` repository.

In this specification, standard control class names are prefixed by " **nc**".  For example, " **ncSwitch**" is the name of a control class that defines an API for controlling a switching function.

## Classes, objects, and APIs

In NCA, each control object has its own control API. The signature of each object's API is completely defined by the class of which it is an instance. The complete API of a Device is exactly equal to the union of the APIs of all the objects it contains.

Thus, if a Controller knows all the objects in a Device, and if it knows the definitions of the classes of which those objects are instances, then it knows the Device's complete API.

In many cases, Controllers will have been custom-built for Devices they control, and will therefore have the required object and class information _a priori._ However, NCA provides rich mechanisms for controllers to acquire such information from the devices themselves, and/or from public online sites.

In reading this specification, it is important to remember that NCA is concerned _only_ with defining Device control and monitoring APIs, not with internal implementation structures that lie behind those APIs in NCA-compliant products.  

## Protocols

A Controller needs a control protocol to access Device control APIs. NCA's Control Model defines APIs abstractly, leaving their realizations in protocols to separate specifications. 

The `NC-Protocol` repository defines a JSON-encoded Remote Procedure Call (RPC) protocol for NCA. Future protocols may implement control APIs in other ways, but all of them will share the same properties, methods, events, and semantics, as defined by the Control Model.

## Blockspecs

In addition to the basic models described above, NCA defines a powerful tool named the _**blockspec**_ for specifying reusable control configurations.  Blockspecs are standardized specifications of NCA device control subassemblies - or even entire devices - that can be adapted and reused to create new devices.  Going forward, the use of blockspecs will reduce engineering cost and increase interoperability of NCA-based products.

Blockspecs are described in [Device Model(Blockspecs and Blockspec IDs)](Device%20Model.md#blockspecs-and-blockspec-ids) and defined formally in the `NC-Blockspecs` repository.
