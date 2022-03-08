# \[Work In Progress\] AMWA MS-05-01: NMOS Control Architecture Framework

[![Lint Status](https://github.com/AMWA-TV/ms-05-01/workflows/Lint/badge.svg)](https://github.com/AMWA-TV/ms-05-01/actions?query=workflow%3ALint)
[![Render Status](https://github.com/AMWA-TV/ms-05-01/workflows/Render/badge.svg)](https://github.com/AMWA-TV/ms-05-01/actions?query=workflow%3ARender)

This repository holds the source for this Specification, part of the family of [Networked Media Open Specifications](https://specs.amwa.tv/nmos) from the [Advanced Media Workflow Association](https://amwa.tv)

<!-- INTRO-START -->

### What does it do?

It defines a system for modelling various types of devices which interrelates with existing NMOS Specifications and the JT-NM Reference Architecture.

### Why does it matter?

The modelling system provides for the definition of device configuration, control and monitoring APIs using a set of published class definitions and operating rules.  In so doing, the system will support at least a basic level of interoperability between compliant devices.

The model includes the abilities for controllers to discover full device APIs in live systems, and into reconfigure devices dynamically (i.e. at run time).  

In all cases, multiple simultaneous controllers are fully supported with no requirement for polling to maintain synchronization.

### How does it work?

The NMOS Control Architecture (NCA) defines control and monitoring class hierarchies and core mechanisms for building device control APIs.  In addition, NCA defines a powerful specification mechanism for optimizing sharing and reuse of NCA control designs across the industry.

<!-- INTRO-END -->

## Getting started

More information about NMOS Specifications and their GitHub repos is at <https://specs.amwa.tv/nmos>.
