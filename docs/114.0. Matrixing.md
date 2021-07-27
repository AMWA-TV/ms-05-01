# 14. Matrixing

Matrixing is an optional feature which may be configured into Devices as needed.

The NCA matrix is a generalization of the familiar crosspoint matrix concept. NCA matrices are rectangular arrays of identical Worker objects that share one or more common input and output busses for the rows and columns, respectively.

A matrix's Worker objects are called _members_ of the matrix. All the members must be instances of the same class.

NCA matrices are instances of the **ncaMatrix** class.

## 14.1. Matrix addressing

**ncaMatrix** members are addressed by coordinates. NCA uses (_x,y_) coordinates, where _x_ is the horizontal (column) number, and _y_ is the vertical (row) number - see `Figure 7`.

| ![Matrix addressing](images/Figure-7.png) |
|:--:|
| *Figure 7. Matrix addressing* |

## 14.2. Complex matrices

When the members of an NCA matrix are switches, the matrix represents a conventional switching matrix. When the members are gain controls, the matrix represents a conventional mixing matrix. However, **ncaMatrix** members can be of any Worker class, even blocks.

For example, `Figure 9` shows a matrix of blocks, where each block is as shown in `Figure 8`.

| ![A complex matrix element](images/Figure-8.png) |
|:--:|
| *Figure 8. A complex matrix element* |

| ![A matrix of complex elements](images/Figure-9.png) |
|:--:|
| *Figure 9. A matrix of complex elements* |

In addition to the matrix and its members, the matrix includes one additional instance of the member class, termed the _matrix proxy_. The matrix proxy is used by Controllers to access the settings of matrix members.

A complete matrix is illustrated in `Figure 10`, below.

| ![Complete ncaMatrix instance, showing matrix proxy](images/Figure-10.png) |
|:--:|
| *Figure 10. Complete ncaMatrix instance, showing matrix proxy* |

To reference a particular member of the matrix, a Controller first calls a **Set(...)** method in the **ncaMatrix** instance to set the member's (x,y) coordinates, then calls the appropriate **Set(...)** method in the matrix proxy to change the value.

The special coordinate value zero denotes an entire row or column. So, for example, (0,2) indicates the entire second row. The controller can use this feature to access many members at once: whole rows (0,n); whole columns (n,0); or the whole matrix, (0,0).

## 14.3. Matrix signal flow

A matrix has input and output signal ports (_matrix ports_) that are separate from the input and output signal ports of its members. Matrix rows have matrix input ports; matrix columns have matrix output ports.

_Multichannel matrices_ are supported. These have more than one input port per row and/or more than one output port per column. Each separate port of a row or column is called a _channel_, and the number of ports the row or column has is called its _channel count._

When multiple member outputs connect to a single matrix output channel, those member outputs are combined into the channel signal output using a method dependent on the matrix implementation.
