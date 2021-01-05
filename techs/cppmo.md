# The Concept and Best Practice of C++ Memory Order

- [The Concept and Best Practice of C++ Memory Order](#the-concept-and-best-practice-of-c-memory-order)
  - [Memory Order Basics](#memory-order-basics)
  - [Atomic Operations and Types in C++](#atomic-operations-and-types-in-c)
    - [Storing a New Value (or not) Depending on the Current Value](#storing-a-new-value-or-not-depending-on-the-current-value)
  - [Synchronizing Operations and Enforcing Ordering](#synchronizing-operations-and-enforcing-ordering)

## Memory Order Basics

Whataver its type, an object is stored in one or more *memory locations*. In C++ memory model,

- Every variable is an object, including those that are members of other object.
- Every object occupies at least one memory location.
- Variables of fundamental type such as `int` or `char` are exactly one memory location, whatever their size, even if they're adjacent or part of an array
- Adjacent bit fields are part of the same memory location

In order to avoid the data race, there has to be an enforced ordering(先后) between the accesses in two threads that access the *same* memory location.

Every object in a C++ program has a defined *modification order* composed of all the writes to that object from all threads in the program, starting with the object's initialization. In most cases this order will vary between runs (because of out of order execution of CPU), but in any given execution of the program, all the threads in the system must agree on the order. If the object in question is not one of the atomic types of C++, you are responsible for ensuring that the necessary synchronization is in place. However, although all threads must agree on the modification orders of each individual object, they don't necessarily have to agree on the relative order of operations on separate objects. 

## Atomic Operations and Types in C++

The standard atomic types are not copyable or assignable in the conventional sense, in that they have to copy constructors or copy assignment operators. All ioerations on an atomic type are defined as atomic, and assignment and copy-construction involves two objects. A single operation on two distincet objects cannot be atomic. In the case of copy-construction or copy-assignment, the value must first be read from one object and then written to the other. These are two separate operations on two separate objects, and the combination cannot be atomic. Therefore, there operations are not permitted. However, they support assignment from and implicit conversion to the corresponding built-in types as well as direct `load()` and `store()` member functions, `exchange()`, `compare_exchange_weak()`, and `compare_exchange_strong()`. Each of the operations on the atomic types has an optional memory-ordering argument that can be used to specify the required memory-ordering semantics. The operations are divided into three categories:

- *Store* operations, which can have `memory_order_relaxed`, `memory_order_release`, or `memory_order_seq_cst` ordering.
- *Load* operations, which can have `memory_order_relaxed`, `memory_order_consume`, `memory_order_acquire`, or `memory_order_seq_cst` ordering.
- *Read-modify-write* operations, which can have `memory_order_relaxed`, `memory_order_consume`, `memory_Order_acquire`, `memory_oreder_release`, `memory_order_acq_rel`, or `memory_order_seq_cst` ordering.

The default ordering for all operations is `memory_order_seq_cst`.

### Storing a New Value (or not) Depending on the Current Value

## Synchronizing Operations and Enforcing Ordering