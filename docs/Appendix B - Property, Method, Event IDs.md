# 16. Appendix B - Property, method, and event IDs

Every class has a set of properties, a set of methods, and a set of events. A classes properties, methods, and events are generically referred to as its **elements.** Each of the three element sets contains elements the class itself defines plus (except for **ncaRoot** ) elements inherited from the class's ancestors.

Every element has an ID (**property ID, method ID, event ID,**or generically**element ID**) in the following format:

```c
struct ncaElementID {
  ncaUint16 DefLevel /* Level of tree where element is defined. 1=root */
  ncaUint16 Index /* Index of element within the class within the level */
} /* Separate index for each of properties, methods, events */
```

By convention, NCA documentation refers to these IDs in the form **LLtNN** , where

- **LL** is a two-digit **DefLevel** ,
- **NN** is a two-digit **Index** , and
- **t** is the type of ID: **p** for property, **m** for method, and **e** for event.

For example, consider a gain-control class **ncaGain**. Referring to the control model in `Figure 1` (see [Class hierarchy](Control%20Model.md#73-class-hierarchy)) and the example `Figure 10` (see [Complex matrices](Matrixing.md#142-complex-matrices)), above, **ncaGain's** ancestry is (using the convention that arrows point to parents):

**ncaRoot ← ncaWorker ← ncaActuator ← ncaGain**

**01 02 03 04**  **←**  **DefLevel values**

**ncaGain** inherits elements from each of its progenitors. Thus, **ncaGain** might have the following elements
 (**this is a simplified example**):

**Type Name ID Defined in**

```
property ClassID 01p01 ncaRoot

method GetClassID(...) 01m01 ncaRoot

event PropertyChanged 01e01 ncaRoot

property Ports 02m01 ncaWorker

method GetPorts(...) 02p01 ncaWorker

method SetPorts(...) 02p02 ncaWorker

[nothing to inherit from this class] ncaActuator

property GainValue 04p01 ncaGain

property GainMax 04p02 ncaGain

property GainMin 04p03 ncaGain

method GetGain(...) 04m01 ncaGain Returns value, max, min

method SetGain(...) 04m02 ncaGain
```

Defining IDs in this way ensures it will always be possible to define specification upgrades and proprietary classes compatibly, without element ID clashes or number address space constraints.
