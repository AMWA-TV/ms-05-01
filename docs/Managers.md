# Managers

NCA defines a set of classes called **managers** that perform central housekeeping functions. Manager classes are **singletons**, i.e. they are instantiated at most once per device.

| **Class name** | **Description** |
| --- | --- |
| **NcManager** | Abstract base class for managers. Never instantiated. All managers inherit from this class. |
| **NcDeviceManager** | Manages information relevant to the whole Device - including manufacturer's model and serial number, Device name and role and overall operating state. |
| **NcSubscriptionManager** | Manages subscriptions, the constructs by which Devices inform Controllers of significant events. |
| **NcClassManager** | Manages class and data type discovery. |
| **NcSecurityManager** | Manages security keys. |
| **NcFirmwareManager** | Reports firmware versions. |
| **NcLockManager** | Provides functions to help Controllers lock objects. |
| **NcDeviceTimeManager** | Collects the device's time-source object(s) and provides access to the device's time-of-day clock. |

*Table 3. Managers* 
