# 15. Appendix A -Class ID format

## 15.1. Class identifiers

A class identifier (classID) shall consist of a lineage key and a version number. Every classID includes a version number to uniquely identify the revision level of the class.

In the following sections, a classID is represented as {n; i1•i2•i3 ...}, where n is the class version number, and i1•i2•i3 ... is the lineage key, as described below.

An annotated example is given in `Figure 11` below.

## 15.2. Lineage keys

Each class shall be identified by a hierarchical key nominally of the form i(1)•i(2)•i(3) ... where each i(n) shall be a parameter that uniquely identifies a class within its siblings at a particular inheritance level of the class tree. Each i(n) is called a _class index_.

The lineage key of a class shall be a set of class indices that identifies the entire lineage of the class, beginning from **ncaRoot** , extending down through all ancestor classes, and ending at the class in question. The key may contain as many class indices as needed to describe the layers of the hierarchy.

Except for i(1), a class index shall be either a simple integer (_class ordinal_) or an _authority key_. i(1) shall always be a simple integer, and its value shall always be **1** , designating **ncaRoot**.

Authority keys are detailed below in **Nonstandard classIDs**.

A class defined by NCA is called a _standard class_. The lineage key of a standard class shall contain no authority keys.

For example, for a standard class **X** whose lineage key is **1•2•12•7** , the lineage key shall be interpreted left-to-right as follows:

• **1** designates **ncaRoot**.

• **1•2** designates a child of **ncaRoot**.

• **1•2•12** designates a child of the class whose parent is class **1•2**.

• **1•2•12•7** designates class **X** , a child of the class whose parent is **1•2•12**.

## 15.3. Nonstandard classIDs

A class not defined by NCA is called a _nonstandard class_, and its classID is called a _nonstandard classID_. A nonstandard classID shall contain at least one authority key.

Nonstandard classes and their classIDs may be created by a manufacturer of a device or by any other organization. The source of a classID is called the class's _authority_. An authority key uniquely identifies an authority.

A nonstandard classID shall be constructed by inserting an authority key into the lineage key immediately before the index of the first nonstandard class. In the following details, authority keys will be indicated by " **A**", as in **{n; i(1)•i(2)•A•i(3) ...}**.

The interpretation of a lineage key that includes an authority key **A** shall be as follows:

1. Every index to the left of **A** shall identify a standard NCA class.
2. Every index to the right of **A** shall identify a nonstandard class defined by the authority identified by **A**.
3. The index immediately to the left of **A** shall identify the class that is the parent of the class whose index is immediately to the right of **A**.

For example, the lineage key of **{1•1•2•A•5}** identifies a nonstandard class whose parent is **{1•1•2}** , whose authority is **A** , and whose own (nonstandard) class index is **5**.

All class indices to the right of the authority key shall be considered to be nonstandard. For example, the lineage key **{1•1•2•A•5•4}** identifies a nonstandard class with index **4** that is a child of the nonstandard class with index **5** that is a child of the standard class **{1•1•2}**.

_Note: This rule is a corollary of the rule that a standard class shall not inherit from a proprietary class._

Choice of class ordinal values to the right of an authority key is the responsibility of the identified authority.

## 15.4. Format details

_These details assume the existence of NCA base datatype definitions for numbers, strings, etcetera._

```c
struct ncaClassID {
  ncaUint16 Version /\* class revision level \*/
  ncaLineageKey Lineage /\* array of class indices, as described above \*/
}

struct ncaLineageKey {
  ncaUint16 Count /* number of fields in lineage key */
  ncaClassIndex Index[] /* array of class indices, as described above */
  /* To form the lineage key, class indices shall be added to the Index[] array in order of inheritance, beginning from ncaRoot, index value 1. */
}

typedef ncaClassIndex variant(ncaUint16, ncaAuthorityKey)

struct ncaAuthorityKey {
  ncaUint16 Sentinel /*always FFFFh */
  ncaUint8 Reserved /* must be zero */
  ncaIEEE_OrgID OrgID /* 24-bit IEEE OUI or CID */
}
```

The IEEE (Institute of Electrical and Electronic Engineers) Public CID and OUI are identifiers that uniquely identify organizations. Any incorporated organization may receive a unique Public CID or OUI from the IEEE upon request and payment of a one-time fee. The difference between the two is that OUIs are used in Ethernet MAC addresses, CIDs are not. The address spaces of the two do not overlap, so they are the same for NCA purposes.

### 15.4.1. Class ordinal values

Class ordinal values may be freely assigned subject to the following rules:

| **Value or range** <br/> (decimal) | (hex) | **Rule** |
| --- | --- | --- |
| 0 | 0000 | Do not use |
| 1...32767 | 0001...7FFF | May be freely used for standard or nonstandard classes |
| 32768...65534 | 8000...FFFE | Reserved for deprecated NCA feature |
| 65535 | FFFF | Special flag identifying the start of an authority key |

## 15.5. Example

| ![Standard and nonstandard class IDs for an ncaGain subtree](images/Figure-11.png) |
|:--:|
| *Figure 11. Standard and nonstandard class IDs for an ncaGain subtree* |
