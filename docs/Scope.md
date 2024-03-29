# AMWA MS-05-01 NMOS Control Architecture

{:.no_toc}

- A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

## Scope and purpose

This document introduces NCA, the **NMOS Control Architecture**. NCA is an architecture for modeling media devices, and controlling and monitoring them over IP networks.

In this and other NCA documents, the term "device control" means "device configuration, control and monitoring" unless otherwise specified. Note that the use of the term "device" in this document does not have exactly the same meaning as it does in earlier NMOS IS-xx specifications. In this document we use the terms "NCA device" and "NMOS device" to make the distinction clear.

NCA's specification is designed to complement and interoperate with the existing family of [NMOS](https://specs.amwa.tv/nmos) specifications.

**NCA's purpose is device control (and status monitoring) only.** NCA does not define higher-level mechanisms such as compound media formats, production role data, studio asset records, facilities reservations, accounting data, performance rights data, and so on and on. Such mechanisms might build (in part) upon NCA device control, but their specification is out of NCA's scope.

**NCA takes as a requirement that media control networks might not be connected to the Internet.** The general operation of NCA does not depend on connections to external data or code resources.

**NCA can apply equally to physical or virtual NCA devices.** In NCA documentation, the term NCA device refers to both unless otherwise stated.

**NCA is not a device or controller implementation specification.** NCA's features and structures serve to define NCA device Application Programming Interfaces (APIs) that can be used for device control, but the internal programming structures behind those APIs are strictly out of NCA's scope.

**NCA is not a user interface specification.** The NCA control model defines numerous elements with application functions (switches, indicators, gain controls etc.), but these elements are embodied only as abstractions in a NCA device control model. How, whether, or when they are represented in a user control interface is out of NCA's scope.

## NCA Documentation Structure

Documentation of the NCA is organized across multiple parts:

1. An **architecture specification** that defines NCA's various concepts, mechanisms, and rules.
1. A [framework specification](https://specs.amwa.tv/ms-05-02) that defines NCA's repertoire of standard control classes and datatypes.
1. At least one [protocol specification](https://specs.amwa.tv/is-12) that defines a way of accessing NCA's control and monitoring features over the network.

### Scope of this document

This document describes the NCA architecture - item (1.) in the preceding section.

### Note on terminology

In NCA documents, the term "control" means "control and monitoring", except in contexts where control and monitoring are explicitly treated separately.

## References

AMWA specifications and documents are referenced by hyperlinks.
