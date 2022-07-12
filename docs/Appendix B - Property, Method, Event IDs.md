# Appendix B - Element IDs
Every class has a set of properties, a set of methods, and a set of events. A classes properties, methods, and events are generically referred to as its **elements.** Each of the three element sets contains elements the class itself defines plus (except for the root object `NcObject`) elements inherited from the class's ancestors.

Every property, method, and event has an ID (**element ID**) of the following form:

```
struct NcElementID {
  ncaUint16 DefLevel    // Level of tree where element is defined. 1=root 
  ncaUint16 Index       // Index of element within the class within the level 
} 
```

By convention, NCA documentation refers to these IDs in the form **LLtNN**, where

- **LL** is a two-digit **DefLevel** ,
- **NN** is a two-digit **Index** , and
- **t** is the type of ID: **p** for property, **m** for method, and **e** for event.

For example, consider a gain-control class `ncaGain`. Referring to the control model (see [Class hierarchy](Control%20Model.md#class-hierarchy)) `ncaGain's` ancestry is (using the convention that arrows point to parents):

`ncaRoot ← ncaWorker ← ncaActuator ← ncaGain`

`ncaGain` inherits elements from each of its ancestors. Thus, it might have the following elements
 (_this is a simplified example_):

|     Type     |        Name     |   ID    | Defined in           |
| ------------ | --------------- | ------- | -------------------- |
| property     | classID         | 01p01   | ncObject             |
| event        | PropertyChanged | 01e01   | ncObject             |
| property     | ports           | 02p01   | ncWorker             |
| property     | setting         | 04p01   | ncaGain              |
|              |                 |         |                      |

Defining IDs in this way ensures it will always be possible to define specification upgrades and proprietary classes compatibly, without element ID clashes or number address space constraints.
