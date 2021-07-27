## 13. Security

The NCA security mechanism provides the following functions (this is aligned with `BCP-003-01`):

**Confidentiality** A control message passing between Devices and Controllers is unreadable to third parties.

**Identification** A Device can confirm that a Controller is owned by a trusted party, and vice versa.

**Integrity** A receiver of a control message can confirm that it has not been tampered with.

**Authentication** Receivers of control messages can confirm that those messages actually come from the Controller it is interacting with, and vice versa.

The NCA security mechanism does not provide **Authorization** `BCP-003-02`. Authorization is the mechanism that determines whether a Controller has sufficient permission to require execution of a given NCA command. NCA Authorization is a separate mechanism, to be defined in future work.

Implementation of NCA Security is required for NCA compliance, but implementation of Authorization is optional.

### 13.1. The ncaSecurityManager class

For the most part, implementation and operation of security measures is a protocol function, and therefore out of scope of this document. However, there are certain common elements of all security implementations that can be vested in architectural mechanisms. NCA defines these mechanisms in the Manager class named ncaSecurityManager.
