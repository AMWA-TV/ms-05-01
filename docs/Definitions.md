# Definitions

Definitions and abbreviations for this draft

**Control** The capability to affect and/or monitor a product's function. In this document, _control_ usually implies _network control_, defined next.

**Network control** The capability to control a product remotely, via a network.

**NCA** NMOS control architecture.

**Device** See "The term _Device_" below.

**Controller** See "The term _Controller_ below."

**Control API** Methods, data, and rules for controlling something (a device, or a component of a device).

**Control class** Pattern for constructing control objects.

**Control object** Realized instance of a control class.

**Control class** or just **subclass**: a class created by inheritance from another ("parent" ) class. Synonym of "child class".

**Control protocol** Definitions of message formats, data flows, data serializations, and exchange rules for accessing control APIs.

**Framework mechanism** Architectural mechanism essential to NCA operation in any type of device. Conformant Devices implement all of the Framework Mechanisms applicable.

**Feature-set mechanism** conditional mechanism required when a device implements a specific feature set.

**Sequence** Written **sequence\<V\>:** an ordered set of values of datatype **V** that is accessed via an index. **V** can be a simple datatype or an object.

**Block** or **block object.** An instance of a block class.

**Block class** Subclass of the class **NcBlock**.

**Block member** An object contained by a block.

**Nested block** A block contained by another given block, as in "block A is nested inside block B".

**Root block** The single fundamental block in a device that contains all other control objects and blocks.

**Owning block** _or_ **owner** The block that contains a given object.

**Touchpoint** Linkage item which indicates the association between an NCA item and an identified resource in another control environment (e.g. NMOS IS-xx).

## The term _Device_

The [NMOS Glossary][NMOS-G] and [JT-NM Reference Architecture][JTNM-RA] define _Device_ as follows:

**Device** _A Device is a block of functionality within a networked media infrastructure. Examples of devices could include: camera, microphone, SDI to IP adapter, chroma keyer, audio mixer._

As NCA is a part of the NMOS family of specification, use of this term is compatible with prior use within NMOS. However, NCA Devices include additional processing and logic functions not described in existing NMOS specifications, which are focused on senders and receivers \[IS-05\], event notification emitters and subscribers \[IS-07\], and routing matrices \[IS-08\].

This document will use the capitalized term **Device** to denote the entity which hosts the NCA Device Model. The uncapitalized version will be used in nonspecific English uses of the term, e.g. "device control". Where a clear distinction is made between IS-04 and NCA devices, the term will be qualified, as in "NCA Device", "IS-04 Device", and so on.

## The term _Controller_

In this document, the capitalized term **Controller** means a programmatic entity for controlling Devices using NCA-specified means. The uncapitalized version will be used for the nonspecific English use of the term, e.g. "controllers for non-NCA environments". Where a clear distinction is made between NCA and non-NCA controllers, the term will be qualified, as in "non-NCA controller", "NCA Controller".

[NMOS-G]: https://specs.amwa.tv/nmos/branches/main/docs/Glossary.html "NMOS Glossary"

[JTNM-RA]: https://www.jt-nm.org/reference-architecture "Joint Taskforce on Network Media Reference Architecture v1.0"
