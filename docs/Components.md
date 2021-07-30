# Components of the architecture

## Models

NCA is based on two interrelated models:

- The **control model**, which describes the mechanisms of NCA control, and the repertoire of elementary NCA control functions used to describe Device control elements. This includes the class definitions and methods.
- The **Device model**, which describes how control and management model elements are instantiated and configured to create the control API that a Device exposes to the network.

The Device model is concerned _only_ with Device control and management APIs, and not internal implementation structures of NCA-compliant products.

NCA defines both the Device model and the control model in object-oriented terms. Thus:

- The control model defines a set of **control classes** (or just **classes**) that can be used to construct **control objects** (or just **objects**);
- The Device model describes how those classes are instantiated and configured to form Devices.

In this document, control class names will be prefixed by " **nca**", e.g. " **ncaSwitch**" might be the name of a control class that exports an API for controlling a switching function.

## Classes, objects, and APIs

In NCA, each object has its own control API. The signature of each object's API is completely defined by the class of which it is an instance. The complete API of a Device is exactly equal to the union of the APIs of all the objects it contains.

Thus, if a Controller knows all the objects in a Device, and if it knows the definitions of the classes of which those objects are instances, then it knows the Device's complete API.

In many cases, Controllers will have been custom-built for Devices they control, and will therefore have the required object and class information _a priori._ However, NCA provides rich mechanisms for controllers to acquire such information from public registries and from the devices themselves.

## Protocols

A controller needs a control protocol to access control APIs. The definition of control protocols is outside the scope of this architecture specification, but protocol definitions are clearly required for the models to be useful.

NCA's control model defines APIs abstractly, but leaves their physical specification to subsequent protocol design phases. Future protocols may implement control APIs in various ways, but all API implementations will share the parameters and semantics defined by the models.

A future phase of the NCA project will define one or more control protocols.
