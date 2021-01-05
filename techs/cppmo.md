# The Concept and Best Practice of C++ Memory Order

- [The Concept and Best Practice of C++ Memory Order](#the-concept-and-best-practice-of-c-memory-order)
  - [Memory Order Basics](#memory-order-basics)
  - [Atomic Operations and Types in C++](#atomic-operations-and-types-in-c)
    - [Storing a New Value (or not) Depending on the Current Value](#storing-a-new-value-or-not-depending-on-the-current-value)
  - [Synchronizing Operations and Enforcing Ordering](#synchronizing-operations-and-enforcing-ordering)
    - [the Synchronizes-with Relationship](#the-synchronizes-with-relationship)
    - [the Happens-before Relationship](#the-happens-before-relationship)
    - [Memory Ordering for Atomic Types](#memory-ordering-for-atomic-types)
    - [Sequentially Consistent Ordering](#sequentially-consistent-ordering)
    - [Non-Sequentially Consistent Memory Orderings](#non-sequentially-consistent-memory-orderings)
    - [Relaxed Ordering](#relaxed-ordering)

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

There is a special operation of atomic type called compare/exchange, and it comes in the form of the `compare_exchange_weak()` and `compare_exchange_strong()` member functions. The compare/exchange operation is the cornerstone of programming with atomic types; it compares the value of the atomic variable with a supplied expected value, and stores the supplied desired value if they are equal. If the values are not equal, the expected value is updated with the actual value of the atomic variable. The return type of the compare/exchange functions is a `bool`, which is `true` if the store was performed and `false` otherwise.

For `compare_exchange_weak()`, the store might not be successful even if the original value was equal to the expected value, in which case the value of the variable is unchanged and the return value of `compare_exchange_weak()` is `false`. This is called a *spurious failure*, because the reason for the failure is a function of timing rather than the values of the variables. Therefore, `compare_exchange_weak()` must typically be used in a loop:

```c++
bool expected = false;
extern atomic<bool> b; // set somewhere else
while (!b.compare_exchange_weak(expected, true) && !expected);
```

On the other hand, `compare_exchange_strong()` is guaranteed to return `false` only if the actual value wasn't equal to the `expected` value. 

## Synchronizing Operations and Enforcing Ordering

Suppose you have two threads, one of which is populating a data structure to be read by the second. The code listing shows such senario:

```c++
#include <vector>
#include <atomic>
#include <iostream>

std::vector<int> data;
std::atomic<bool> data_ready(false);

void reader_thread() {
    while (!data_ready.load()) { // (1)
        std::this_thread::sleep(std::milliseconds(1));
    }
    std::cout << "The answer = " << data[0] << "\n"; // (2)
}

void writer_thread() {
    data.push_back(42); // (3)
    data_ready = true; // (4)
}
```

The requied enforced access ordering comes from the operations on the `std::atomic<bool>` variable `data_ready`; they provide the necessary ordering by virtue of the memory model releations *happens-before* and *synchronizes-with*. The write of the data (3) happens-before the write to the `data_ready` flag (4), and the read of the flag (1) happens-before the read of the data (2). When the value read from `data_ready` (1) is `true`, the write synchronizes-with that read, creating a happens-before relationship. Because the happens-before is transitive, the write to the data (3) happens-before the wrute to the flag (4), which happens-before the read of the `true` value from the flag (1), which happens before the read of the data (2), and you have an enforced ordering: the write of the data happens-before the read of the data and everything is OK. With default atomic operations, the operation that writes a value happens before an operation that reads that value, which is rather intuitive, but it does need spelling out: the atomic oprations also have other options for the ordering requirements.

### the Synchronizes-with Relationship

The basic idea is this: a suitably tagged atomic write operation `W` on a variable `x` synchronizes-with a suitably tagged atomic read operation on `x` that reads the value stored by (我们在以下情况下，认为写和读是同步的):

- that write (`W`) (读操作读出的是`W`对`x`写入的值)
- a subsequent atomic write operation on `x` by the same thread that performed the initial write `W` (读操作读出的是`W`所在的线程在`W`操作之后写入的值)
- a sequence of atomic read-modify-write operations on `x` (such as `fetch_add()` or `compare_exchange_weak()`) by any thread, where the value read by the first thread in the sequence is the value written by `w`. (读操作读出的是任何线程通过read-modify-write操作对`x`写入的值，而这个read-modify-write读出的值是`W`写入的值).

### the Happens-before Relationship

The *happens-before* relationship is the basic building block of operation ordering in a program; it specifies which operations see the effects of which other operations. For a single thread, it's largely straightforward: if one operation is sequenced before another, then it also happens-before it. If operation A on one thread inter-thread happens-before operation B on another thread, then A happens-before B. If operation A in one thread synchronizes-with operation B in another thread, then A inter-thread happens-before B.

### Memory Ordering for Atomic Types

There are six memory ordering options that can be applied to operations on atomic types: `memory_order_relaxed`, `memory_order_consume`, `memory_order_acquire`, `memory_order_release`, `memory_order_acq_rel`, and `memory_order_seq_cst`. The default memory-ordering option for all operations on atomic types is `memory_order_seq_cst` unless explicitly specified. Although there are six ordering options, they represent three models: *sequentially consistent* ordering (`memory_order_seq_cst`), *acquire-release* ordering (`memory_order_consume`, `memory_order_acquire`, `memory_order_release`, and `memory_order_acq_rel`), and *relaxed* ordering (`memory_order_relaxed`). 

### Sequentially Consistent Ordering

The default ordering is named *sequentially consistent* because it implies that the behavior of the program is consistent with a somple sequential view of the world. If all iperations on instances of atomic types are sequentially consistent, the behavior of a multithreaded program is as if all these operations were performed in some particular sequence by a single thread: all threads must see the same order of operations.

From the point of view of synchronization, a sequentially consistent store synchronizes-with a sequentially consistent load of the same variable that reads the value stored. What's more, any sequentially consistent atomic operations done after that load must also appear after the store to other threads in the system using sequentially consistent atomic operations. The following listing shows sequentially consistency in action:

```c++
#include <atomic>
#include <thread>
#include <cassert>

std::atomic<bool> x, y;
std::atomic<int> z;

void write_x() {
    x.store(true, std::memory_order_seq_cst); // (1)
}

void write_y() {
    y.store(true, std::memory_order_seq_cst); // (2)
}

void read_x_then_y() {
    while (!x.load(std::memory_order_seq_cst));
    if (y.load(std::memory_order_seq_cst)) // (3)
      ++z;
}

void read_y_then_x() {
    while (!y.load(std::memory_order_seq_cst));
    if (x.load(std::memory_order_seq_cst)) // (4)
      ++z;
}

int main() {
    x = false;
    y = false;
    z = 0;
    std::thread a(write_x);
    std::thread b(write_y);
    std::thread c(read_x_then_y);
    std::thread d(read_y_then_x);
    a.join();
    b.join();
    c.join();
    d.join();
    assert(z.load() != 0); // (5_)
}
```

The `assert` (5) can never fire, because either the store to `x` (1) or the store to `y` (2) must happens first by the view of all threads, even though it's not specified which.

### Non-Sequentially Consistent Memory Orderings

Probably the biggest issue outside the nice sequentially consistent world is the fact that *there's no longer a single global order of events*. This means that different threas can see different views of the same operations, and you must throw away any mental model you have of operations from different threads neatly interleaved one after the other. Not only do you have to account for things happening truly concurrently, but *threads don't have to agree on the order of events*. It's not just that the compiler can reorder the instructions. Even if the threads are running the same bit of code, they can disagree on the order of events caused by operations in other threads in the absence of explicit ordering constraints, because the different CPU caches and internal buffers can hold different values for the same memory. It's so important I'll say it again: *threads don't have to agree on the order of events*.

You also have to throw out mental models based on the idea of the compiler or processor reordering the instructions. *In the absence of other ordering constraints, the only requirement is that all threads agree on the modification order of each individual variables.* Operations on distinct variables can appear in different orders on different threads, provided the values seen are consistent with any additional ordering constaints imposed.

### Relaxed Ordering

Operations on atomic types performed with relaxed ordering don't participate in synchronizes-with relationships. Operations on the same variable within a single thread still obey happens-before relationships, but there is almost no requirement on ordering relative to other threads. The only requirement is that accesses to a single atomic variable from the same thread can't be reordered; onace a given thread has seen a particular value of an atomic variable, a subsequent read by that thread can't retrieve an earlier value of the variable. Without any additional synchronization, the modification order of each variable is the only thing shared between threads that are using `memory_order_relaxed`. 

To demonstrate just how relaxed your relaxed operations can be, you need only two threads, as shown in the following listing.

```c++
#include <atomic>
#include <thread>
#include <cassert>

void write_x_then_y() {
    x.store(true, std::memory_order_relaxed); // (1)
    y.store(true, std::memory_order_relaxed); // (2)
}

void read_y_then_x() {
    while (!y.load(std::memory_order_relaxed)); // (3)
    if (x.load(std::memory_order_relaxed)) // (4)
        ++z;
}

int main() {
    x = false;
    y = false;
    z = 0;
    std::thread a(write_x_then_y);
    std::thread b(read_y_then_x);
    a.join();
    b.join();
    assert(z.load() != 0); // (5)
}
```

This time the assert (5) *can* fire, because the load of *x* (4) can read `false`, even though the load of *y* (3) reads `true` and the store of `x` (1) happens-before the store of `y` (2). `x` and `y` are different variables, so there are no ordering guarantees relating to the visibility of values arising from operations on each.