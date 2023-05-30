# Control Model

The NCA Control Model is a singly-inherited tree of classes. Each NCA class defines a particular control or monitoring API that devices implement.

NCA [control classes](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#control-classes) can have three kinds of elements:

- **Properties**. These are control and status parameters.

- **Methods** **.** Methods represent actions a Controller can perform on the object.

- **Events**. Events are defined conditions that arise inside objects. For example, all objects include the [PropertyChanged](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/NcObject.html#propertychanged-event) event, which is raised (i.e. occurs) when the value of a property of the object has changed.

The event and subscription mechanism is described further in [Events, Notifications and Subscriptions](Core%20Mechanisms.md#events-notifications-and-subscriptions).

NCA classes are interface-defining classes that imply nothing about the programming classes inside actual products. Some Device developers can elect to use object-oriented programming environments with software class structures that mirror the NCA class tree, while others can take entirely different approaches. Such choices are outside NCA's scope.

Each property, method, and event of a class has a unique identifier. These identifiers (_element identifiers_) are described in the [Identification](Identification.md).

## Class inheritance

The NCA control model defines a class inheritance mechanism, in which the various classes' definitions are derived from the definitions of other, more elementary classes, with everything ultimately leading up (if you think of ancestry diagrams) or down (if you think of trees) to a single fundamental base named [NcObject](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncobject). Thus, a control class is the outcome of a **class inheritance hierarchy**.

When a class inherits from another class, _all_ of its properties, methods, and events are inherited. A notable example of this mechanism is the **PropertyChanged** event. **PropertyChanged** is defined in [NcObject](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/NcObject.html#propertychanged-event). All classes in the model inherit directly or indirectly from [NcObject](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncobject) ; therefore, they all have **PropertyChanged** events. Consequently, a controller can subscribe to property-value changes for any object in the NCA device.

_This document will visualize the class inheritance hierarchy as a tree in inverted form, with NcObject at the top, and successive refinements of NcObject extending downward. Our tree will be a trailing vine._

### Inheritance rules

Over time, the NCA-defined tree of classes will grow. Additionally, product developers will likely extend the public-defined tree with proprietary classes to cover product-specific functions. To cover both of these cases, NCA has expansion rules that ensure the tree expands in a compatible manner, in which new classes, whether arising from NCA growth or proprietary extension, coexist harmoniously with legacy ones.

These rules are:

1. New classes are defined by inheritance from existing ones.

1. A new ("child") class MUST implement all the properties, methods, and events of the class ("parent") from which it inherits.

1. A child class can expand its parent's definition by defining additional properties, methods, and/or events.

1. A child class's inherited elements MUST retain the respective element IDs (see [Identification](Identification.md)) from its parent class.

## Class hierarchy

For NCA to meet its interoperability objectives, a repertoire of common control class models are defined in the [NMOS Control Framework](https://specs.amwa.tv/ms-05-02). These classes are defined in the NCA control model, via an inheritance hierarchy of control classes that descends from [NcObject](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncobject).

NCA Control classes are specified in several categories and subcategories. Each category or subcategory has its own base class that inherits directly or indirectly from [NcObject](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncobject). All the classes in a category inherit from that category's base class. Thus, each category is a subtree of the overall hierarchy.

Figure **NCA Control Model** illustrates the NCA class tree, and shows the categories.

| ![NCA Control Model](images/ControlModel.png) |
|:--:|
| _**NCA control model**_ |

### Class categories

The categories shown in figure **NCA Control Model** are as follows:

- The **Blocks** subtree contains the definition of [NcBlock](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncblock) - see [Blocks](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Blocks.html).

- The [Workers](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#workers) subtree contains definitions of classes that perform processing or monitoring functions.
- The [Managers](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#ncmanager) subtree contains definitions of classes that perform Device housekeeping functions.

## Datatype definitions

To support the class definitions, the control model also contains an extensive set of [datatype definitions](https://specs.amwa.tv/ms-05-02/branches/v1.0-dev/docs/Framework.html#datatypes). These definitions specify the scalar and structural datatypes used to define class properties and method parameters.
