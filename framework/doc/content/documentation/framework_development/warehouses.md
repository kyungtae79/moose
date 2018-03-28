# Warehouses

The warehouse objects (based on [MooseObjectWarehouseBase](https://github.com/idaholab/moose/blob/devel/framework/include/base/MooseObjectWarehouseBase.h)) in MOOSE hold onto the objects that are built by the factory.  They are templated so they can hold any of the types of objects the Factory can build.

The main purpose is to accelerate retrieval of sets of objects needed during assembly.  To this end, they have many accessors on them such as `getActiveObjects()`.  Internally, these sets of objects are cached to make retrieval quick.

## MooseObjectWarehouse

[MooseObjectWarehouse](https://github.com/idaholab/moose/blob/devel/framework/include/base/MooseObjectWarehouse.h) is based on `MooseObjectWarehouseBase` but adds important functionality for objects that support the [SetupInterface](https://github.com/idaholab/moose/blob/devel/framework/include/base/SetupInterface.h).  In addition, there is a bit of functionality there for holding sets of Kernels that go with each `MooseVariable` to make retrieving them quick for Jacobian calculations.

There is some trickiness in `addObject()`.  To support `getActiveVariableBlockObjects()` we want to have a `Warehouse` for each variable Kernel's are acting on.  To do that, we have nested `MooseObjectWarehouse`s which we add to in `addObject()`.  However, we need the recursion to stop after one level so we can pass `recurse = false` to keep it from recursing to death.