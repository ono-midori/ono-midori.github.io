# Memory Management of C++ Containers

- [Memory Management of C++ Containers](#memory-management-of-c-containers)
  - [malloc, free, new, and delete](#malloc-free-new-and-delete)
    - [malloc and free](#malloc-and-free)
    - [new and delete](#new-and-delete)
    - [Four Versions of Global New](#four-versions-of-global-new)
    - [Overload New and Delete for Class](#overload-new-and-delete-for-class)
    - [Per-Class Allocator](#per-class-allocator)
    - [Usage of Embedded Pointer to Avoid Cookie in Per-Class Allocator](#usage-of-embedded-pointer-to-avoid-cookie-in-per-class-allocator)
  - [Static Allocators](#static-allocators)

## malloc, free, new, and delete

`malloc` and `free` are the keywords in C language to allocate and release memory. `new` and `delete` are C++ keywords for object creation and destruction. 

### malloc and free

`void* malloc(int nbytes)` allocates *n* bytes of dynamic memory from heap, or free store. Malloc invokes system call to request memory from Linux kernel, which adopts sophisticated algorithm to management dynamic memory. `free(p)` releases the memory allocated at the pointer `p`, and `p` should be returned from a successful `malloc` call. The performance of malloc and free is controlled by user.

The Linux kernel maintains the number of bytes allocated by `malloc` using additional memory area called cookie, such that the `free` operation could release the correct length of memory. Some memory allocation algorithms are designed without using cookie in order to save memory usage.

### new and delete

The `new` function call constructs an object on the free store. The function first allocates memory using `malloc`, and the number of bytes can be learned from the sizeof the object type (for non-POD object, how to determine the length of memory allocated?), and then calls the constructor of the object (perhaps by placement-new operation). Array new (`new T[n]`) firstly allocates *n* objects of memory, and then constructs *n* objects at the correct location. The `delete` function call destroys a previously `new`ed object. It first calls the destructor of the object, and then release the memory to kernel. Array delete (`delete[]`) firstly calls the destructor one-by-one, and then release the memory space of all objects back to the kernel. It is a rule that the array delete and the array new must be paired. If an array new is paired with a raw delete, the memory space of *n* object is still returned to the kernel, but only the destructor of the first object is invoked. If all the object is POD type, the destructor is trivial, and the situation is benign. On the other hand, if the operations in the destructor are non-trivial, some resource may be leaked.

### Four Versions of Global New

```c++
/* new_delete2.cpp */
#include <iostream>

using namespace std;

class Bad {};

class Foo {
public:
  Foo() { cout << "Foo::Foo()" << endl; }

  Foo(int) {
    cout << "Foo::Foo(int)" << endl;
    throw Bad();
  }

  void *operator new(size_t size) { // normal version
    cout << "operator new(size_t size), size=" << size << endl;
    return malloc(size);
  }

  void *operator new(size_t size, void *start) { // placement new
    cout << "operator new(size_t size, start), size=" << size
         << ", start=" << start << endl;
    return start;
  }

  // the first parameter must be size_t type
  void *operator new(size_t size, long extra) {
    cout << "operator new(size_t size, long extra), size=" << size
         << ", extra=" << extra << endl;
    return malloc(size + extra);
  }

  void *operator new(size_t size, long extra, char init) {
    cout << "operator new(size_t size, long extra, char init), size=" << size
         << ", extra=" << extra << ", init=" << init << endl;
    return malloc(size + extra);
  }

  // error
  /*
  void* operator new(long extra, char init) {
          return malloc(extra);
  }
  */

  void operator delete(void *, size_t) {
    cout << "operator delete(void*, size_t)" << endl;
  }

  void operator delete(void *, void *) {
    cout << "operator delete(void*, void*)" << endl;
  }

  void operator delete(void *, long) {
    cout << "operator delete(void*, long)" << endl;
  }

  void operator delete(void *, long, char) {
    cout << "operator delete(void*, long, char)" << endl;
  }
};

int main() {
  Foo start;
  cout << "1" << endl;
  Foo *p1 = new Foo; // normal new
  cout << "2" << endl;
  Foo *p2 = new (&start) Foo; // placement new
  cout << "3" << endl;
  Foo *p3 = new (100) Foo; // new(size_t, long)
  cout << "4" << endl;
  Foo *p4 = new (100, 'a') Foo; // new(size_t, long, char)
  cout << "5" << endl;
  Foo *p5 = new (100) Foo(1); // new(size_t, long) with ctor(int)
  cout << "6" << endl;
  Foo *p6 = new (100, 'a') Foo(1); // new(size_t, long, char) with ctor(int)
  cout << "7" << endl;
  Foo *p7 = new (&start) Foo(1); // placement new with ctor(int)
  cout << "8" << endl;
  Foo *p9 = new Foo(1); // ctor(int)
}
```

### Overload New and Delete for Class

The `new` and `delete` operators of class can be overloaded, so that we can customize the memory management operations. For example, this code recipe overload `new`, `delete`, `new []`, and `detele[]` operator for class `Foo`.

```c++
/* new_delete.cpp */
#include <iostream>
#include <string>

using namespace std;

class Foo {
public:
  int _id;
  long _data;
  string _str;

  Foo() : _id(0) {
    cout << "default ctor. this=" << this << " id=" << _id << endl;
  }

  Foo(int i) : _id(i) {
    cout << "ctor. this=" << this << " id=" << _id << endl;
  }

  ~Foo() { // non-trivial dtor.
    cout << "dtor. this=" << this << " id=" << _id << endl;
  }

  static void *operator new(size_t size);
  static void operator delete(void *pdead, size_t size);
  static void *operator new[](size_t size);
  static void operator delete[](void *pdead, size_t size);
};

static void *Foo::operator new(size_t size) {
  Foo *p = (Foo *)malloc(size);
  cout << "operator new, p=" << p << endl;
  return p;
}

static void Foo::operator delete(void *pdead, size_t size) {
  cout << "operate delete, pdead=" << pdead << endl;
  free(pdead);
}

static void *Foo::operator new[](size_t size) {
  Foo *p = (Foo *)malloc(size);
  cout << "operator new[], p=" << p << endl;
  return p;
}

static void Foo::operator delete[](void *pdead, size_t size) {
  cout << "operator delete[], pdead=" << pdead << endl;
  free(pdead);
}
```

First we ignore the customized new and delete operator, and explicitly invoke the global new and delete:

```c++
int main() {
  cout << "sizeof(Foo)=" << sizeof(Foo) << endl;
  Foo *p = ::new Foo(7);
  ::delete p;

  Foo *pArray = ::new Foo[5];
  ::delete[] pArray;
}
```

We get output as follow:

```shell
$ ./new_delete
sizeof(Foo)=48
ctor. this=0x1e53280 id=7
dtor. this=0x1e53280 id=7
default ctor. this=0x1e532c8 id=0
default ctor. this=0x1e532f8 id=0
default ctor. this=0x1e53328 id=0
default ctor. this=0x1e53358 id=0
default ctor. this=0x1e53388 id=0
dtor. this=0x1e53388 id=0
dtor. this=0x1e53358 id=0
dtor. this=0x1e53328 id=0
dtor. this=0x1e532f8 id=0
dtor. this=0x1e532c8 id=0
```

Then we invoke the customized new and delete operation:

```c++
int main() {
  cout << "sizeof(Foo)=" << sizeof(Foo) << endl;
  Foo *p = new Foo(7);
  delete p;

  Foo *pArray = new Foo[5];
  delete[] pArray;
}
```

More information is printed:

```shell
$ ./new_delete
sizeof(Foo)=48
operator new, p=0x194d280
ctor. this=0x194d280 id=7
dtor. this=0x194d280 id=7
operate delete, pdead=0x194d280
operator new[], p=0x194d2c0
default ctor. this=0x194d2c8 id=0
default ctor. this=0x194d2f8 id=0
default ctor. this=0x194d328 id=0
default ctor. this=0x194d358 id=0
default ctor. this=0x194d388 id=0
dtor. this=0x194d388 id=0
dtor. this=0x194d358 id=0
dtor. this=0x194d328 id=0
dtor. this=0x194d2f8 id=0
dtor. this=0x194d2c8 id=0
operator delete[], pdead=0x194d2c0
```

We find that in array new memory layout, there is 8 bytes between the pointers of array and the first object. Why?

### Per-Class Allocator

In the previous example, we directly called `malloc` and `free` in the overridden version of `new` and `delete`. Here we customize the memory allocation procedure:

```C++
/* allocator1.cpp */
#include <cstddef>
#include <iostream>

using namespace std;

class Screen {
public:
  Screen(int x) : i(x) {}
  int get() { return i; }

  void *operator new(size_t);
  void operator delete(void *, size_t);

private:
  Screen *next;
  static Screen *freeStore;
  static const int screenChunk;

  int i;
};

// per class allocator

void *Screen::operator new(size_t size) {
  Screen *p;
  if (!freeStore) {
    size_t chunk = screenChunk * size; // 24 elements
    freeStore = p = reinterpret_cast<Screen *>(new char[chunk]);
    // split the chunk into small pieces and linked them together
    for (; p != &freeStore[screenChunk - 1]; ++p)
      p->next = p + 1;
    p->next = 0;
    cout << "allocate new chunk" << endl;
  }

  p = freeStore;
  freeStore = freeStore->next;
  return p;
}

void Screen::operator delete(void *p, size_t) {
  static_cast<Screen *>(p)->next = freeStore;
  freeStore = static_cast<Screen *>(p);
}

Screen *Screen::freeStore = 0;
const int Screen::screenChunk = 24;

int main() {

  cout << "sizeof(Screen) = " << sizeof(Screen) << endl;

  size_t const N = 100;
  Screen *p[N];
  for (int i = 0; i < N; ++i)
    p[i] = new Screen(i);

  for (int i = 0; i < 10; ++i)
    cout << p[i] << endl;

  for (int i = 0; i < N; ++i)
    delete p[i];
}
```

The `new` operator actually utilizes preallocated chunks of memory, such that creating 100 `Screen` object only invokes 5 times of `malloc` operations.

```shell
$ ./allocator1
sizeof(Screen) = 16
allocate new chunk
allocate new chunk
allocate new chunk
allocate new chunk
allocate new chunk
0xeb3280
0xeb3290
0xeb32a0
0xeb32b0
0xeb32c0
0xeb32d0
0xeb32e0
0xeb32f0
0xeb3300
0xeb3310
```

If we changed the code to use global `new` and `delete` operator:

```c++
int main() {

  cout << "sizeof(Screen) = " << sizeof(Screen) << endl;

  size_t const N = 100;
  Screen *p[N];
  for (int i = 0; i < N; ++i)
    p[i] = ::new Screen(i);

  for (int i = 0; i < 10; ++i)
    cout << p[i] << endl;

  for (int i = 0; i < N; ++i)
    ::delete p[i];
}
```

We find that the addresses between to adjacent objects are increased from 16 to 32. 

```shell
$ ./allocator1
sizeof(Screen) = 16
0x9fc280
0x9fc2a0
0x9fc2c0
0x9fc2e0
0x9fc300
0x9fc320
0x9fc340
0x9fc360
0x9fc380
0x9fc3a0
```

### Usage of Embedded Pointer to Avoid Cookie in Per-Class Allocator

```c++
// allocator2.cpp
#include <iostream>

using namespace std;

class Airplane {
private:
  struct AirplaneRep {
    unsigned long miles;
    char type;
  };

  union {
    AirplaneRep rep;
    Airplane *next; // embedded pointer
  };

public:
  unsigned long getMiles() { return rep.miles; }

  char getType() { return rep.type; }

  void set(unsigned long m, char t) {
    rep.miles = m;
    rep.type = t;
  }

  static void *operator new(size_t size);
  static void operator delete(void *deadObject, size_t size);

private:
  static const int BLOCK_SIZE;
  static Airplane *headOfFreeList;
};

Airplane *Airplane::headOfFreeList;
const int Airplane::BLOCK_SIZE = 512; // memory chunk size

void *Airplane::operator new(size_t size) {
  if (size != sizeof(Airplane)) // only happens on inheritance
    return ::operator new(size);

  Airplane *p = headOfFreeList;
  // set the free list header, because the current is used
  if (p)
    headOfFreeList = p->next;

  else { // allocate a new chunk of memory
    Airplane *newBlock =
        static_cast<Airplane *>(::operator new(BLOCK_SIZE * sizeof(Airplane)));

    // configure the linked list
    for (int i = 1; i < BLOCK_SIZE - 1; ++i)
      newBlock[i].next = &newBlock[i + 1];

    // set the last linked list element
    newBlock[BLOCK_SIZE - 1].next = 0;

    p = newBlock;
    headOfFreeList = &newBlock[1];
  }

  return p;
}

void Airplane::operator delete(void *deadObject, size_t size) {
  if (deadObject == 0)
    return;

  if (size != sizeof(Airplane)) {
    ::operator delete(deadObject);
    return;
  }

  Airplane *carcass = static_cast<Airplane *>(deadObject);

  carcass->next = headOfFreeList;
  headOfFreeList = carcass;
  // never free. Recycle memory to free list.
}

int main() {
  cout << "sizeof(Airplane) = " << sizeof(Airplane) << endl;

  size_t const N = 100;
  Airplane *p[N];

  for (int i = 0; i < N; ++i)
    p[i] = new Airplane;

  p[1]->set(1000, 'A');
  p[5]->set(2000, 'B');
  p[9]->set(500000, 'C');

  for (int i = 0; i < 10; ++i)
    cout << p[i] << endl;

  for (int i = 0; i < N; ++i)
    delete p[i];
}
```

From the output of code, we find that the distance of addresses between two adjacent objects is exactly the size of object. On the other hand, no extra space is needed for the `next` pointer of linked list. The memory region of `next` pointer is overwritten by the object data when it is constructed.

```shell
$ ./allocator2
sizeof(Airplane) = 16
0xc82280
0xc82290
0xc822a0
0xc822b0
0xc822c0
0xc822d0
0xc822e0
0xc822f0
0xc82300
0xc82310
```

## Static Allocators

Designing customized `new` and `delete` operations for each class is reinventing the wheels. If a general static allocator is designed, each class could reuse the code for memory allocation and release.

```c++
#include <complex>
#include <iostream>
#include <string>

class allocator {
private:
  struct obj {
    struct obj *next; // embedded pointer
  };

public:
  void *allocate(size_t);
  void deallocate(void *, size_t);

private:
  obj *freeStore = nullptr;
  const int CHUNK = 5;
};

void *allocator::allocate(size_t size) {
  obj *p;

  if (!freeStore) {
    std::cout << "allocate chunk of memory" << std::endl;
    size_t chunk = CHUNK * size;
    freeStore = p = reinterpret_cast<obj *>(malloc(chunk));

    for (int i = 0; i < CHUNK - 1; ++i) {
      p->next = reinterpret_cast<obj *>((char *)p + size);
      p = p->next;
    }
    p->next = nullptr;
  }
  p = freeStore;
  freeStore = freeStore->next;
  return p;
}

void allocator::deallocate(void *p, size_t) {
  reinterpret_cast<obj *>(p)->next = freeStore;
  freeStore = reinterpret_cast<obj *>(p);
}

class Foo {
public:
  long L;
  std::string str;
  static allocator myAlloc;

  Foo(long l) : L(l) {}
  static void *operator new(size_t size) { return myAlloc.allocate(size); }

  static void operator delete(void *pdead, size_t size) {
    return myAlloc.deallocate(pdead, size);
  }
};

allocator Foo::myAlloc;

class Goo {
public:
  std::complex<double> c;
  std::string str;
  static allocator myAlloc;

  Goo(const std::complex<double> &x) : c(x) {}

  static void *operator new(size_t size) { return myAlloc.allocate(size); }

  static void operator delete(void *pdead, size_t size) {
    return myAlloc.deallocate(pdead, size);
  }
};

allocator Goo::myAlloc;

int main() {
  {
    Foo *p[100];

    std::cout << "sizeof(Foo) = " << sizeof(Foo) << std::endl;
    for (int i = 0; i < 23; ++i) {
      p[i] = new Foo(i);
      std::cout << p[i] << ' ' << p[i]->L << std::endl;
    }

    for (int i = 0; i < 23; ++i)
      delete p[i];
  }
  {
    Goo *p[100];
    std::cout << "sizeof(Goo) = " << sizeof(Goo) << std::endl;
    for (int i = 0; i < 17; ++i) {
      p[i] = new Goo(std::complex<double>(i, i));
      std::cout << p[i] << ' ' << p[i]->c << std::endl;
    }

    for (int i = 0; i < 17; ++i)
      delete p[i];
  }
}
```
The output of code is somewhat verbose. Firstly, the distance of addresses beween two adjacent objects are still the size of object.

```shell$ ./allocator3 
sizeof(Foo) = 40
allocate chunk of memory
0x13f1280 0
0x13f12a8 1
0x13f12d0 2
0x13f12f8 3
0x13f1320 4
allocate chunk of memory
0x13f1350 5
0x13f1378 6
0x13f13a0 7
0x13f13c8 8
0x13f13f0 9
allocate chunk of memory
0x13f1420 10
0x13f1448 11
0x13f1470 12
0x13f1498 13
0x13f14c0 14
allocate chunk of memory
0x13f14f0 15
0x13f1518 16
0x13f1540 17
0x13f1568 18
0x13f1590 19
allocate chunk of memory
0x13f15c0 20
0x13f15e8 21
0x13f1610 22
sizeof(Goo) = 48
allocate chunk of memory
0x13f1690 (0,0)
0x13f16c0 (1,1)
0x13f16f0 (2,2)
0x13f1720 (3,3)
0x13f1750 (4,4)
allocate chunk of memory
0x13f1790 (5,5)
0x13f17c0 (6,6)
0x13f17f0 (7,7)
0x13f1820 (8,8)
0x13f1850 (9,9)
allocate chunk of memory
0x13f1890 (10,10)
0x13f18c0 (11,11)
0x13f18f0 (12,12)
0x13f1920 (13,13)
0x13f1950 (14,14)
allocate chunk of memory
0x13f1990 (15,15)
0x13f19c0 (16,16)
```

