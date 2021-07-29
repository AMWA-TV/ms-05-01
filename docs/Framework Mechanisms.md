# 10. Framework Mechanisms

By "framework mechanisms", we mean architectural mechanisms that are essential to the operation of NCA in all devices. Framework mechanisms are distinguished from **optional mechanisms** that may be configured into schemas as needed.

## 10.1. Control Traffic

NCA traffic between Controllers and Devices consists of three kinds of control messages:

- A **Command** is a call to a method of an object. Commands travel from a requesting Controller to a controlled object.
- A **Response** is a controlled object's returned result from a Command. The Response travels from the controlled object to the requesting Controller.
- A **Notification** is an object's announcement of an event occurrence. Notifications travel from the controlled object to all of the Controllers that have subscribed to the given event.

NCA defines a Controller as a software function, not as a physical entity, and a Device as a real or virtual construct. The command travel paths listed above refer to communication of information between software entities, not necessarily physical pieces of equipment.

The actual means of control message transport and the formats of the messages are determined by the protocol being used. NCA protocols are out of scope of this document.

## 10.2. Control sessions

NCA control is session-oriented, which implies the following:

1. Requests and responses occur in correlated (but not blocking) pairs.
2. Controlled Devices (and their inventory of objects) have persistent application relationships with Controllers.
3. Prompt discovery of Device failure or communication failure is a required feature of the protocol.
4. Devices shall report changing parameters to subscribing Controllers. Subscriptions are valid through the conclusion of the control session unless explicitly un-subscribed.
5. When a communication failure occurs, the control session behaviour shall be well-defined. Whether the session survives or gracefully dies is a property of the protocol in use.

## 10.3. Events, Notifications, and Subscriptions

NCA supports the use of multiple simultaneous Controllers. Those Controllers might be coordinated - in the case of a Controller redundancy architecture, for example - or they might be uncoordinated - for example, several Controllers from different makers, controlling different but somewhat overlapping aspects of system functionality.

Multi-controller applications are more common than it might seem, because a physical Device's front panel controls constitute a Controller. Thus, **every case in which NCA controls Devices that have their own physical controls is a multi-controller application.**

In all multiple control point situations, timely tracking of parameter and state updates is required for error-free Device control. NCA's core update-tracking mechanism is the **event** **.** An event is anything that happens inside a Device that a Controller may need to know about.

One way Controllers can discover Device changes is to poll them. A goal of NCA is to render polling completely unnecessary in all cases. To this end, NCA events generate **notifications**_._ A notification is a spontaneous message from a Device to one or more Controllers that announces the occurrence of an event and gives its particulars.

Not all Controllers need to know about all events. When a Controller wants to be informed of the occurrence of a certain event in a certain object, it registers a **subscription**with a housekeeping object named **ncaSubscriptionManager.** A subscription causes a **notification message** to be delivered to the subscribing Controller whenever the designated event is raised in the designated object.

Once registered, a subscription remains in effect for the life of the control session, unless the Controller cancels it.

### 10.3.1. Notification messages

A notification message is essentially a callback command whose parameters depend on the type of event. However, unlike actual commands, a notification does not require a response.

The parameters of notifications depend on the type of event. In general, event parameters are designed to give full detail about the event, so that Controllers receiving events can act on them immediately, without having to query the Device further.

### 10.3.2. Notification delivery

The precise method by which notifications are delivered back to Controllers is a protocol issue, and out of scope of this document. However, for efficiency, the control model does identify two abstract ways of notification delivery. These are identified as _reliable_ and _fast_. **Reliable** delivery uses the same delivery method as Commands and Responses; **Fast** delivery uses lower-overhead, lower-reliability methods.

**Fast** delivery may support multicasting; if so, Devices are allowed to multicast notifications for events to which there are multiple subscribers.

Controllers specify the type of delivery separately for each subscription. This allows them to elect **Reliable** delivery for critical events such as error alerts, and **Fast** delivery for noncritical events such as signal level sensor updates.

The control model says nothing about how **Reliable** and **Fast** delivery schemes are implemented. Their implementation will vary from protocol to protocol. In some cases, the two may be the same. **Fast** may or may not be able to multicast. The control model only defines the abstraction, not the realization.

### 10.3.3. The PropertyChanged event

The most common event is the **PropertyChanged** event, which is defined by **ncaRoot** , and so inherited by every object. **PropertyChanged** is the key mechanism for NCA's support of multiple Controllers without polling. By subscribing to changes in the properties it cares about, a Controller can stay in sync with the device, even when those properties are being changed by other controllers. The specifics of the subscription mechanism and methods for subscribing to subtree events are a subject of future work.

## 10.4. Controller hierarchies

Some NCA-controlled media networks may contain thousands of devices. In such cases, it will not be practical for a single central Controller to maintain a separate control session with each device. To address this problem, indirect control using a hierarchy of Controllers may be implemented, with successive levels aggregating control functionality in ways appropriate to the applications.

## 10.5. Concurrency control

NCA supports applications that use multiple simultaneous Controllers in which a particular object may receive directives from more than one Controller.

In such applications, certain application control events require the exchange of more than one control message. Therefore, the potential for a race condition exists. To prevent race conditions, NCA supports single-threading via a simple object-locking mechanism with the elements listed below.

An NCA lock is a mutual exclusion ("mutex") mechanism designed to prevent some or all access to the object by anyone other than the lockholder.

The NCA lock mechanism is for transient programmatic use. It is not:

- An authorization/permission mechanism for controlling user access to Devices or their features;
- A reservation mechanism for securing user access to Devices or features at a later time; or
- An operations mechanism for restricting Device access during maintenance, or when problems are occurring, or for other facility management purposes.

The NCA mechanism supports two levels of lock, as follows:

1. **NoReadWrite** locks, which prevent all access to the object by non-lockholders - read requests while in this state will return a "not-ready" type of status.
2. **NoWrite** locks, which prevent non-lockholders from modifying object data, but allow them to retrieve it.

The owner of a lock (lockholder) is the Controller that sets the lock. Locks are expected to be ephemeral.

The lock interface is in the **ncaRoot** class, and therefore is inherited by every other NCA class. However, non-lockable objects may be defined, if desired, as described below.

Locking rules are defined as follows, subject to the data definitions in the YANG control model spec:

1. An object may be implemented as **lockable** or **non-lockable**. A non-lockable object shall return the **NotImplemented** status codes from all locking-related commands.
2. The lockability or an object shall be indicated by the value of its **Lockable** property. **Lockable** is inherited from **ncaRoot**.
3. A lockable object shall be locked by at most one lockholder at a time.
4. A locked object may be locked **NoReadWrite** or locked **NoWrite** , but not both.
5. An object locked **NoReadWrite** may only be read or written by the lockholder.
6. An object locked **NoWrite** may be read by any Controller but only written by the lockholder.
7. A lockholder may upgrade its lock from **NoWrite** to **NoReadWrite** , or downgrade its lock from **NoReadWrite** to **NoWrite, or** remove its lock entirely.
8. No lock survives a Device reset.
9. Controller to Device communication is session-oriented. When a Controller's session ends for any reason, the Device must automatically remove all locks set by that Controller session.
10. It is possible to lock an entire Device by locking its Device Manager object (see [Managers](112.0.%20Managers.md)), provided that none of the device's other objects are locked.
11. Locked objects may not be deleted.

## 10.6. Capability enumeration

To discover the detailed capabilities of a block, a Controller enumerates its objects and its signal paths.

### 10.6.1. Object enumeration

We refer to an object contained by a block as a _member_ of that block. **ncaBlock** has the following methods for enumerating a block's members:

**GetMembers(...)** Returns the list of members.

**GetMembersRecursive(...)** Returns the list of members of the block and all contained blocks.

**FindMembersByPath(...)** Finds members of the given block, given a path specification that may include wildcards.

**FindMembersByPathRecursive(...)** Finds members of the given block and all contained blocks, given a path specification that may include wildcards.

**FindMembersByUserLabelRecursive(...)** Finds members of the given block and all contained blocks, given a user label value that may include wildcards.

### 10.6.2. Signal path enumeration

**ncaBlock** defines two methods Controllers can use to enumerate signal paths:

**GetSignalFlow(...)** Returns the block's internal signal flow.

**GetSignalFlowsRecursive(...)** Returns the internal signal flows of the given block and all contained blocks.

These functions return simple lists of signal-path descriptors. Each signal-path descriptor specifies the ports at each end of the signal path.

## 10.7. Time and media clocking

NCA allows an arbitrary number of independent media clocks per device. These media clocks can be synchronized to various kinds of internal and external time references. As well, NCA supports a Device time-of-day clock that can also be synchronized to an external time reference. An example is in `Figure 6`.

| ![Device with multiple media clocks and time sources](images/Figure-6.png) |
|:--:|
| *Figure 6. Device with multiple media clocks and time sources* |

In the NCA control model, each external or internal time reference is represented by an instance of class **ncaTimeSource** , each internal media clock is represented by an instance of class **ncaMediaClock** , and the Device time of day clock is represented by the singleton housekeeping object **NCA deviceTimeManager.**

## 10.8. Networking

The NCA networking mechanism has two kinds of network interfaces: _control network interfaces_, which are the network interfaces that receive and transmit control protocol traffic, and _media transport network interfaces_, which receive and transmit media streams.

The class structure is as follows:

1. **ncaBaseNetwork** is an abstract base class that defines the common elements of all networks.
2. **ncaControlNetworkInterface** is a child of **ncaBaseNetwork** for control protocol traffic.
3. **ncaMediaTransportNetworkInterface** is a child of **ncaBaseNetwork** for media streaming traffic.

These classes will be defined in detail in future phases of work.

## 10.9. Reliability

NCA enables implementations reliable enough to satisfy:

- Critical live-to-air and studio production requirements
- Legal requirements for broadcast system reliability, including emergency broadcast requirements
- Major live performance and presentation events requirements

NCA's architecture supports reliable implementations with the following features:

1. **Multiple redundant control networks**
2. **Multiple redundant media transport streams and networks**
3. **Control traffic security**

NCA control traffic security features (see [Security](113.0.%20Security.md)) reduce the chance of control failure due to malicious activity.

4. **Fully-acknowledged command/response architecture**

All NCA commands are acknowledged. No open-ended mechanisms (e.g. multicasting) are used for critical protocol traffic.

5. **Poll-free design**

All asynchronous device changes can be discovered by event subscription; no Controller polling is required. With correct implementation, no polling-related race conditions are possible.

6. **Device supervision**

See [Device supervision](110.0.%20Framework%20Mechanisms.md#1091-device-supervision).

7. **Secure Device reset capability**

See [Device reset](110.0.%20Framework%20Mechanisms.md#1092-device-reset).

NCA protocols (defined in future work) may support additional reliability features - for example, Ethernet-based protocols can support spanning-tree Ethernet and/or can use reliable data transport protocols such as TCP.

### 10.9.1. Device supervision

Devices are continuously supervised with the aid of **heartbeat messages**. Such messages shall be defined as part of each NCA protocol specification.

Device supervision ensures that a control session (see [Control sessions](110.0.%20Framework%20Mechanisms.md#102-control-sessions)) is not retained after either of its endpoints disappears, while simultaneously allowing the case that the control session might (protocol specific) be allowed to persist in the case of temporary communication failure.

The exact format of heartbeat messages and the specific rules of the Device supervision mechanism are protocol-specific.

### 10.9.2. Device reset

The NCA Device reset function allows systems to recover effectively from catastrophic errors. A Device reset has the effect of returning the affected Device to the state it was in immediately following power-up.

Device reset is an optional feature.

A Device reset is invoked when it receives a _Device-reset command_.

- A Device-reset command may use a special protocol data unit with a simple, recognizable format that can be handled by low-level recovery code in devices.
- Every Device reset command should include mechanisms to prevent malicious or inadvertent resets.