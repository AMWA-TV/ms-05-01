# Components of the architecture

## Models

NCA is based on two interrelated models:

- The **control model**, also called the **NCA Framework**, which describes the mechanisms of NCA control, and the repertoire of  NCA control and monitoring functions. The control model is specified as a set of control classes which are defined using the Web IDL language and are available in the `NC-Framework` repository.

- The **Device model**, which describes how control and management model elements are instantiated and configured to create a Device's network control API.  Definitions of particular Device models and Device model elements are contained in JSON files known as _**blockspecs**_. The JSON schemas defining the blockspec format are available in the `NC-Blockspecs` repository.

These models are concerned _only_ with defining Device control and monitoring APIs,  not with internal implementation structures that lie behind those APIs in NCA-compliant products.

In this specification, standard control class names are prefixed by " **nca**".  For example, " **ncaSwitch**" is the name of a control class that defines an API for controlling a switching function.

## Classes, objects, and APIs

In NCA, each control object has its own control API. The signature of each object's API is completely defined by the class of which it is an instance. The complete API of a Device is exactly equal to the union of the APIs of all the objects it contains.

Thus, if a Controller knows all the objects in a Device, and if it knows the definitions of the classes of which those objects are instances, then it knows the Device's complete API.

In many cases, Controllers will have been custom-built for Devices they control, and will therefore have the required object and class information _a priori._ However, NCA provides rich mechanisms for controllers to acquire such information from the devices themselves, and/or from public online sites.

## Protocols

A Controller needs a control protocol to access Device control APIs. NCA's Control Model defines APIs abstractly, leaving their realizations in protocols to separate specifications. 

The document [NC-Protocol](Protocol.md) defines a JSON-encoded Remote Procedure Call (RPC) protocol for NCA.  Future protocols may implement control APIs in other ways, but all of them will share the same properties, methods, events, and semantics, as defined by the Control Model.