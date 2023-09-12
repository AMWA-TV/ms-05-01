# Core Mechanisms

By "Core mechanisms", we mean architectural mechanisms that are essential to the operation of NCA in all devices.

## Control Traffic

NCA traffic between Controllers and Devices consists of three kinds of control messages:

- A **command** is a call to a method of an object. Commands travel from a requesting Controller to a controlled object.
- A **response** is a controlled object's returned result from a Command. The Response travels from the controlled object to the requesting Controller.
- A **notification** is an object's announcement of an event occurrence. Notifications travel from the controlled object to all of the Controllers that have subscribed to the given event.

The actual message formats and means of message transport are determined by the protocol being used.

## Control sessions

NCA control is session-oriented, which implies the following:

1. Requests and responses occur in correlated (but not blocking) pairs.
1. Controlled Devices (and their inventory of objects) have persistent application relationships with Controllers.
1. Prompt discovery of Device failure or communication failure is a required feature of the protocol.
1. Devices report changing parameters to subscribing Controllers as defined by the protocol in use.
1. When a communication failure occurs, the control session behaviour is defined by the protocol in use.

## Events, Notifications, and Subscriptions

NCA supports the use of multiple simultaneous Controllers. Those Controllers might be coordinated - when a system has redundant Controllers, for example - or they might be uncoordinated - for example, when a system includes Controllers from multiple manufacturers.

A common multiple Controller case arises when a network-controlled device includes a non-blank front panel. The front panel is a separate Controller. Thus, every case in which NCA controls Devices that have their own physical controls is a multiple-controller application.

In all multiple-controller situations, timely tracking of parameter and state updates is required for error-free Device control. NCA's update-tracking mechanism is the **event.** An event is anything that happens inside a Device that a Controller may need to know about.

Historically, controllers have often tracked device changes by polling. A goal of NCA is to render polling completely unnecessary in all cases. To this end, NCA events generate **notifications**. A notification is a spontaneous message from a Device to one or more Controllers that announces the occurrence of an event and gives its particulars.

Not all Controllers need to know about all events. When a Controller wants to be informed of the occurrence of a certain event in a certain object, it registers a **subscription** with the Device. A subscription causes a **notification message** to be delivered to the subscribing Controller whenever the designated event is raised in the designated object.

Once registered, a subscription remains in effect for the life of the control session, unless the Controller cancels it or the control session ends.

Subscriptions and their implementation are protocol-specific.

### Notification messages

A notification message is essentially a callback command whose parameters depend on the type of event. However, unlike actual commands, a notification does not require a response.

The parameters of notifications depend on the type of event. In general, event parameters are designed to give full details about the event, so that Controllers receiving events can act on them immediately, without having to query the Device further.

### The PropertyChanged event

The [PropertyChanged](https://specs.amwa.tv/ms-05-02/branches/v1.0.x/docs/NcObject.html#propertychanged-event) event is defined by the base class [NcObject](https://specs.amwa.tv/ms-05-02/branches/v1.0.x/docs/Framework.html#ncobject), so it is inherited by every object.

[PropertyChanged](https://specs.amwa.tv/ms-05-02/branches/v1.0.x/docs/NcObject.html#propertychanged-event) is the key mechanism for NCA's support of multiple Controllers without polling. By subscribing to changes in the properties it cares about, a Controller can stay in sync with the device, even when those properties are being changed by other controllers.

## Capability enumeration

To discover the detailed capabilities of a block, a Controller can enumerate its contained members. Furthermore, the Controller can discover details of the classes from which those objects are built and the data types used.

### Object enumeration

We refer to an object contained by a block as a _member_ of that block. [NcBlock](https://specs.amwa.tv/ms-05-02/branches/v1.0.x/docs/Blocks.html) offers means of retrieving the descriptors of its members.

### Class discovery

If a Controller does not have _a priori_ knowledge of a device's class definitions, it can discover the details of those definitions using the _class discovery_ mechanism. This mechanism returns descriptors to the Controller for each class and datatype the device uses. These descriptors are sufficient for the Controller to construct well-formed API calls to the device; however, they do not describe the semantics of such calls.

The class discovery mechanism is available through the [NcClassManager](https://specs.amwa.tv/ms-05-02/branches/v1.0.x/docs/Managers.html#class-manager).

## Reliability

NCA enables implementations reliable enough to satisfy:

- Critical live-to-air and studio production requirements
- Legal requirements for broadcast system reliability, including emergency broadcast requirements
- Major live performance and presentation events requirements

NCA's architecture supports reliable implementations with the following features:

1. **Multiple redundant control networks**
2. **Multiple redundant media transport streams and networks**
3. **Control traffic security**. NCA control traffic security features (see [Security](Security.md)) reduce the chance of control failure due to malicious activity.
4. **Fully-acknowledged command/response architecture**. All NCA commands are acknowledged. No open-ended mechanisms (e.g. multicasting) are used for critical protocol traffic.
5. **Poll-free design**. All asynchronous device changes can be discovered by event subscription; no Controller polling is required. With correct implementation, no polling-related race conditions are possible.
6. **Device supervision**. See [Device supervision](Core%20Mechanisms.md#device-supervision).

### Device supervision

Device supervision ensures that a control session (see [Control sessions](Core%20Mechanisms.md#control-sessions)) is not retained after either of its endpoints disappears, while simultaneously allowing the case that the control session might (protocol specific) be allowed to persist in the case of temporary communication failure.

Device supervision implementations are protocol-specific.
