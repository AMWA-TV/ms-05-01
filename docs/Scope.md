# AMWA MS-05-01 NMOS Control Architecture \[Work In Progress\]

{:.no_toc}

- A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

## Scope and purpose

This document introduces NCA, the **NMOS Control Architecture**. NCA is an architecture for modeling media devices, and controlling and monitoring them over IP networks.

In this and other NCA documents, the term "device control" means "device configuration, control and monitoring" unless otherwise specified. Note that the use of the term "device" in this document does not have exactly the same meaning as it does in earlier NMOS IS-xx specifications. In this document we use the terms "NCA device" and "NMOS device" to make the distinction clear.

NCA's specification is designed to complement and interoperate with the existing family of [NMOS][] specifications, particularly [IS-04][], [IS-05][], [IS-07][], and [IS-08][].

**NCA's purpose is device control (and status monitoring) only.** NCA does not define higher-level mechanisms such as compound media formats, production role data, studio asset records, facilities reservations, accounting data, performance rights data, and so on and on. Such mechanisms might build (in part) upon NCA device control, but their specification is out of NCA's scope.

**NCA takes as a requirement that media control networks might not be connected to the Internet.** The general operation of NCA does not depend on connections to external data or code resources. Manufacturers may choose to supply controllers and/or devices that depend on wide-area connections to libraries, cloud services, and so on, but such choices will be application decisions not mandated by NCA.

**NCA can apply equally to physical or virtual NCA devices.** In NCA documentation, the term NCA device refers to both unless otherwise stated.

**NCA is not a device or controller implementation specification.** NCA's features and structures serve to define NCA device Application Programming Interfaces (APIs) that can be used for device control, but the internal programming structures behind those APIs are strictly out of NCA's scope.

**NCA is not a user interface specification.** The NCA control model defines numerous elements with application functions (switches, indicators, gain controls, etcetera), but these elements are embodied only as abstractions in a NCA device control model. How, whether, or when they are represented in a user control interface is out of NCA's scope.

## NCA Documentation Structure

Documentation of the NCA is organized across multiple parts:

1. An **architecture specification** that describes NCA's various models, mechanisms, and rules.
1. A [framework specification](https://specs.amwa.tv/ms-05-02) that defines NCA's repertoire of control and monitoring elements.
1. A [blockspec specification](https://specs.amwa.tv/ms-05-03) that defines the format and semantics of _blockspecs_, which are specifications of specific functional groups of NCA control elements for particular application purposes.
1. At least one [protocol specification](https://specs.amwa.tv/is-12) that defines a way of accessing NCA's control and monitoring features over the network.

Each specification may have multiple sections that include textual documentation and formal specifications written in various definition languages.

NCA anticipates that the blockspec mechanism (3, above) will support the growth and evolution of a  set of common models and mechanisms for typical devices and device elements found in practice. Using well-defined common blockspecs will optimize interoperability between control systems and devices, and reduce engineering cost.

### Scope of this document

This document describes the NCA architecture - item (1.) in the preceding section.

### Note on terminology

In NCA documents, the term "control" means "control and monitoring", except in contexts where control and monitoring are explicitly treated separately.

## References

AMWA specifications and documents are referenced by hyperlinks.

IETF RFC documents are referenced by constructs of the form **\[RFC n\]** where n is the RFC number.
