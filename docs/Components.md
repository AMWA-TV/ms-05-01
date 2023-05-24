# Components of the architecture

## Models

NCA is based on two interrelated models:

- The [Framework](https://specs.amwa.tv/ms-05-02), which defines the repertoire of NCA control and monitoring functions and the common mechanisms that they use. The Framework has two components:

  - **Control model:** An inheritance tree of control classes that specify NCA's control and monitoring repertoire. These classes are summarized in the [Control Model](Control%20Model.md) and defined normatively in the framework collection of [model definitions](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html).

  - **Core Mechanisms:** Common mechanisms used by the control classes. These are defined in [Core Mechanisms](Core%20Mechanisms.md).

- The **Device model**, which describes how control model elements are instantiated and configured to create a Device's network control API. The Device model is described in [Device Model](Device%20Model.md).

## Classes, objects, and APIs

In NCA, each control object has its own control API. The signature of each object's API is completely defined by the class of which it is an instance. The complete API of a Device is exactly equal to the union of the APIs of all the objects it contains.

Thus, if a Controller knows all the objects in a Device, and if it knows the definitions of the classes of which those objects are instances, then it knows the Device's complete API.

In some cases, Controllers will have been custom-built for Devices they control, and will therefore have the required object and class information _a priori._ However, NCA provides rich mechanisms for controllers to acquire such information from the devices themselves.

In reading this specification, it is important to remember that NCA is concerned _only_ with defining Device control and monitoring APIs, not with internal implementation structures that lie behind those APIs in NCA-compliant products.

## Protocols

A Controller needs a control protocol to access Device control APIs. NCA's Control Model defines APIs abstractly, leaving their realizations in protocols to separate specifications.

All NCA compliant protocols share the same traits as defined by the [Control Model](Control%20Model.md).
