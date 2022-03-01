# Framework Mechanisms

By "framework mechanisms", we mean architectural mechanisms that are essential to the operation of NCA in all devices. Framework mechanisms are distinguished from **optional mechanisms** that may be configured into schemas as needed.

## Control Traffic

NCA traffic between Controllers and Devices consists of three kinds of control messages:

- A **Command** is a call to a method of an object. Commands travel from a requesting Controller to a controlled object.
- A **Response** is a controlled object's returned result from a Command. The Response travels from the controlled object to the requesting Controller.
- A **Notification** is an object's announcement of an event occurrence. Notifications travel from the controlled object to all of the Controllers that have subscribed to the given event.

NCA defines a Controller as a software function, not as a physical entity, and a Device as a real or virtual construct. The command travel paths listed above refer to communication of information between software entities, not necessarily physical pieces of equipment.

The actual means of control message transport and the formats of the messages are determined by the protocol being used. A JSON-based RPC protocol is available in the `NC-Protocol` repository.

## Control sessions

NCA control is session-oriented, which implies the following:

1. Requests and responses occur in correlated (but not blocking) pairs.
1. Controlled Devices (and their inventory of objects) have persistent application relationships with Controllers.
1. Prompt discovery of Device failure or communication failure is a required feature of the protocol.
1. Devices shall report changing parameters to subscribing Controllers. Subscriptions are valid through the conclusion of the control session unless explicitly un-subscribed.
1. When a communication failure occurs, the control session behaviour shall be well-defined. Whether the session survives or gracefully dies is a property of the protocol in use.

## Events, Notifications, and Subscriptions

NCA supports the use of multiple simultaneous Controllers. Those Controllers might be coordinated - when a system has redundant Controllers, for example - or they might be uncoordinated - for example, when a system includes Controllers from multiple manufacturers.

A common application of multiple controllers arises when a network-controlled device includes a non-blank front panel.  The front panel is a separate Controller. Thus, every case in which NCA controls Devices that have their own physical controls is a multi-controller application.

In all multiple multi-controller situations, timely tracking of parameter and state updates is required for error-free Device control. NCA's core update-tracking mechanism is the **event.** An event is anything that happens inside a Device that a Controller may need to know about.

Historically, Controllers have often tracked device changes by polling. A goal of NCA is to render polling completely unnecessary in all cases. To this end, NCA events generate **notifications**.  A notification is a spontaneous message from a Device to one or more Controllers that announces the occurrence of an event and gives its particulars.

Not all Controllers need to know about all events. When a Controller wants to be informed of the occurrence of a certain event in a certain object, it registers a **subscription** with the Device's _subscription manager,_ an instance of the class **ncaSubscriptionManager.**  A subscription causes a **notification message** to be delivered to the subscribing Controller whenever the designated event is raised in the designated object.

Once registered, a subscription remains in effect for the life of the control session, unless the Controller cancels it or the Controller's session ends.

### Notification messages

A notification message is essentially a callback command whose parameters depend on the type of event. However, unlike actual commands, a notification does not require a response.

The parameters of notifications depend on the type of event. In general, event parameters are designed to give full detail about the event, so that Controllers receiving events can act on them immediately, without having to query the Device further.

### The PropertyChanged event

The most common event is the **PropertyChanged** event.  **PropertyChanged**   is defined by the base class **ncaObject**, so it is inherited by every object. 

**PropertyChanged** is the key mechanism for NCA's support of multiple Controllers without polling. By subscribing to changes in the properties it cares about, a Controller can stay in sync with the device, even when those properties are being changed by other controllers. 

## Controller hierarchies

Some NCA-controlled media networks may contain thousands of devices. In such cases, it will not be practical for a single central Controller to maintain a separate control session with each device. To address this problem, indirect control using a hierarchy of Controllers can be implemented, with successive levels aggregating control functionality in ways appropriate to the applications.  NCA's core mechanisms are sufficient to support such configurations, but the exact implementation details are out of this specification's scope.

## Concurrency control

  Sometimes, application control actions will require the exchange of more than one control message. If two or more controllers are simultaneously attempting to perform such actions on the same set of objects, the potential for a race condition exists. To prevent race conditions, NCA contains features to enforce single-threaded operation when necessary.

An NCA _**lock**_ is a mutual exclusion ("mutex") capable of preventing some or all access to the object by anyone other than the lockholder.

The NCA mechanism supports two levels of lock, as follows:

1. **NoReadWrite** locks, which prevent all access to the object by non-lockholders - read requests while in this state will return a "not-ready" type of status.
2. **NoWrite** locks, which prevent non-lockholders from modifying object data, but allow them to retrieve it.

The owner of a lock (lockholder) is the Controller that sets the lock. Locks are expected to be ephemeral.

The lock interface is in the **ncaObject** class, and therefore is inherited by every other NCA class. However, non-lockable objects may be defined, if desired, as described below.

Locking rules are defined as follows, subject to the data definitions in the YANG control model spec:

1. An object may be implemented as **lockable** or **non-lockable**. A non-lockable object shall return the **NotImplemented** status codes from all locking-related commands.
2. The lockability or an object shall be indicated by the value of its **Lockable** property. **Lockable** is inherited from **ncaObject**.
3. A lockable object shall be locked by at most one lockholder at a time.
4. A locked object may be locked **NoReadWrite** or locked **NoWrite** , but not both.
5. An object locked **NoReadWrite** may only be read or written by the lockholder.
6. An object locked **NoWrite** may be read by any Controller but only written by the lockholder.
7. A lockholder may upgrade its lock from **NoWrite** to **NoReadWrite** , or downgrade its lock from **NoReadWrite** to **NoWrite, or** remove its lock entirely.
8. No lock survives a Device reset.
9. Controller to Device communication is session-oriented. When a Controller's session ends for any reason, the Device must automatically remove all locks set by that Controller session.
10. It is possible to lock an entire Device by locking its Device Manager object (see [Managers](Managers.md)), provided that none of the device's other objects are locked.
11. Locked objects may not be deleted.

The NCA lock mechanism is for transient programmatic use. It is not:

- An authorization/permission mechanism for controlling user access to Devices or their features;
- A reservation mechanism for securing user access to Devices or features at a later time; or
- An operations mechanism for restricting Device access during maintenance, or when problems are occurring, or for other facility management purposes.

## Capability enumeration

To discover the detailed capabilities of a block, a Controller can enumerate its objects and signal paths.  As well, the Controller can discover details of the classes from which those objects are built and the data types used.

### Object enumeration ###

We refer to an object contained by a block as a _member_ of that block. **ncaBlock** has the following methods for enumerating a block's members:

**GetMembers(...)** Returns the list of members.

**GetMembersRecursive(...)** Returns the list of members of the block and all contained blocks.

**FindMembersByPath(...)** Finds members of the given block, given a path specification that may include wildcards.

**FindMembersByPathRecursive(...)** Finds members of the given block and all contained blocks, given a path specification that may include wildcards.

**FindMembersByUserLabelRecursive(...)** Finds members of the given block and all contained blocks, given a user label value that may include wildcards.

### Signal path enumeration

**ncaBlock** defines two methods Controllers can use to enumerate signal paths:

**GetSignalFlow(...)** Returns the block's internal signal flow.

**GetSignalFlowsRecursive(...)** Returns the internal signal flows of the given block and all contained blocks.

These functions return simple lists of signal-path descriptors. Each signal-path descriptor specifies the ports at each end of the signal path.

### Class discovery ###

If a Controller does not have _a priori_ knowledge of a device's class definitions, it may discover the details of those definitions using the NCA _class discovery_ mechanism.  This mechanism returns descriptors to the Controller for each class and datatype the device uses.  These descriptors are sufficient for the Controller to construct well-formed API calls to the device; however, they do not describe the semantics of such calls.

## Reliability ##

NCA enables implementations reliable enough to satisfy:

- Critical live-to-air and studio production requirements
- Legal requirements for broadcast system reliability, including emergency broadcast requirements
- Major live performance and presentation events requirements

NCA's architecture supports reliable implementations with the following features:

1. **Multiple redundant control networks**
2. **Multiple redundant media transport streams and networks**
3. **Control traffic security**

NCA control traffic security features (see [Security](Security.md)) reduce the chance of control failure due to malicious activity.

4. **Fully-acknowledged command/response architecture**

All NCA commands are acknowledged. No open-ended mechanisms (e.g. multicasting) are used for critical protocol traffic.

5. **Poll-free design**

All asynchronous device changes can be discovered by event subscription; no Controller polling is required. With correct implementation, no polling-related race conditions are possible.

6. **Device supervision**

See [Device supervision](Framework%20Mechanisms.md#device-supervision).

7. **Secure Device reset capability**

See [Device reset](Framework%20Mechanisms.md#device-reset).

NCA protocols (defined in future work) may support additional reliability features - for example, Ethernet-based protocols can support spanning-tree Ethernet and/or can use reliable data transport protocols such as TCP.

### Device supervision ###

Devices are continuously supervised with the aid of **heartbeat messages**. Such messages shall be defined as part of each NCA protocol specification.

Device supervision ensures that a control session (see [Control sessions](Framework%20Mechanisms.md#control-sessions)) is not retained after either of its endpoints disappears, while simultaneously allowing the case that the control session might (protocol specific) be allowed to persist in the case of temporary communication failure.

The exact format of heartbeat messages and the specific rules of the Device supervision mechanism are protocol-specific.

### Device reset

The NCA Device reset function allows systems to recover effectively from catastrophic errors. A Device reset has the effect of returning the affected Device to the state it was in immediately following power-up.

Device reset is an optional feature.

A Device reset is invoked when it receives a _Device-reset command_.

- A Device-reset command may use a special protocol data unit with a simple, recognizable format that can be handled by low-level recovery code in devices.
- Every Device reset command should include mechanisms to prevent malicious or inadvertent resets.
