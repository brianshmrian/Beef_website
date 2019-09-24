+++
title = "Initialization"
weight = 2
+++

## Initialization
For class initialization semantics, first the object is zeroed-out, then the root class field initializers are executed in declaration order, then the root class constructor is run, then execution proceeds to the derived class's field initializers and constructor and so on. Virtual method calls will dispatch to the fully derived type even when invoked in a base class's constructor, which can result in a method being called in a type whose constructor has not yet executed.

For struct initialization semantics, the struct is not automatically zeroed out -- the fields initializers and constructor together must fully initialize all the struct fields. Users of a struct can also choose to not execute a constructor and just manually initialize all the fields directly. Uses of structs that are not fully initialized will be disallowed via simple static analysis, which can be overriden via the explicit "?" uninitialized expression.

```C#
/* In this case, we know "UseVec" will initialize the Vector2 value so we use '?' to avoid a "Not initialized" error */
Vector2 vec = ?;
UseVec(&vec);
```

Arrays initilization at allocation time is also supported.

```C#
/* Zero-initialize ending 7 elements */
int[] iArr = new int[10] (1, 2, 3, );

/* Leave ending 7 elements uninitializezd */
int[] iArr = new int[10] (1, 2, 3, ?);

/* Throws an initializer size mismatch error - ending comma is required to zero-initialize if desired */
int[] iArr = new int[10] (1, 2, 3);
```

Types can also define static fields and static constructors. Static initialization order is defined by alphanumeric order of the fully-qualified type name. This order can be overriden with type attributes such as [StaticInitPriority(...)] and [StaticInitAfter(...)]. Static initialization that refer to static fields in other types will cause that type's static initializers to execute on-demand before the access. This on-demand initialization can cause circular dependencies, however, which are resolved by simply skipping any reentrant initializer calls.
