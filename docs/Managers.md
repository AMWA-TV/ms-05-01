# Managers

NCA defines a set of classes called **managers**that perform central housekeeping functions. Some of these classes are mandatory, some optional. Manager classes are **singletons**, i.e. they are instantiated at most once per device.

A tentative list of NCA manager classes are listed in `Table 3`. In this table, the "Req Lvl" (Requirements Level) column values are:

**n/a** Not applicable, the class is never instantiated

**M** Mandatory for NCA compliance

**O** Optional

**S** Mandatory for Devices that have media streaming capability, optional otherwise.

| **Class name** | **Description** | **Req Lvl** |
| --- | --- | --- |
| **ncaManager** | Abstract base class for managers. Never instantiated. All managers inherit from this class. | **n/a** |
| **ncaDeviceManager** | Manages information relevant to the whole Device - including manufacturer's model and serial number, Device name and role, overall operating state, and Device update lock. | **M** |
| **ncaSecurityManager** | Manages security keys. | **M** |
| **ncaFirmwareManager** | Reports firmware versions. May be extended to perform firmware updating. | **M** |
| **ncaSubscriptionManager** | Manages subscriptions, the constructs by which Devices inform Controllers of significant events. | **M** |
| **ncaLockManager** | Provides functions to help Controllers lock objects. | **M** |
| **ncaNetworkManager** | Collects the device's network interface object(s). | **S** |
| **ncaMediaClockManager** | Collects the device's media clock object(s). | **S** |
| **ncaDeviceTimeManager** | Collects the device's time-source object(s) and provides access to the device's time-of-day clock. | **O** |
| **ncaSignalProcessingManager** | Manages global signal processing parameters. | **O** |
| **ncaTaskManager** | Manages Tasks. The Task mechanism is a future feature that will be defined in a later phase of NCA development. | **O** |
| **ncaCommandSetManager** | Manages CommandSets. The Command sets mechanism is a future feature that will be defined in a later phase of NCA development. | **O** |
| **ncaPowerManager** | Manages Device power state, including multiple power supplies, battery supplies, and so on. | **O** |
| **ncaDiagnosticManager** | Provides functions to aid in network system setup and diagnosis. Will normally be extended with device-type-specific features. | **O** |

*Table 3. Managers* 
