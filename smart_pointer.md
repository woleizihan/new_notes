# C++ Smart Pointers (Fluent{C++})

## Smart pointer basics
### The stack and the heap

#### The stack
- Allocating on the stack is the default way to store objects in C++
- Objects allocated on the stack are automatically destroyed when they go out of scope
- There are three ways for an object to go out of scope:
  - next closing bracket
  - `return` statement
  - exception thrown that is not caught inside the current scope

#### The heap
- the heap is where dynamically allocated objects are stored, that is to say objects that are allocated with a call to `new`
- we have to destroy the objects manually
- once ande only once `delete`:
  - not called: never freed, memory leak
  - more than once: undefined behavior

### RAII
- wrap a resource (a pointer for instance) into an object, and dispose of the resources in its desructor
- we can manipulate smart pointers as objects allocated on stack because the destructor will be called when the smart pointers go out of range

## `unique_ptr`, `shared_ptr`, `weak_ptr`, `scoped_ptr`, raw pointers

sorted by decreasing order of usefulness
- `std::unique_ptr`
- raw pointer
- `std::shared_ptr`
- `std::weak_ptr`
- `boost::scoped_ptr`
- `std::auto_ptr`

### `std::unique_ptr`
- this is the smart pointer to use by default
- it is the sole owner of a memory resource and no one else will delete this pointer except the `unique_ptr` that is returned by the function
- even when you receive a `unique_ptr`, you are not guaranteed that no one else has access to this pointer. To solve this, we can use a unique pointer to `const`
  - `std::unique_ptr<const House> buildAHouse()`
  - therefore `unique_ptr` itself cannot be copied and the object it points to, also cannot be modified
  - the ownership can however be transferred from one `unique_ptr` to another by moving a `unique_ptr` into another
    - pass or return from a function
    - `move`

### Raw pointers
- raw pointers and references represent access to an object, but not ownership
- `unique_ptr` represents ownership of an object, so when we need to pass the object to an interface, we pass a reference to the pointed object so the ownership remains the same.

### `std::shared_ptr`
- a single memory resource can be held by several `shared_ptr` at the same time
- allows copies but with a reference-counting mechanism to make sure that every resource is deleted once and only once
- should not be used at default:
  - several simultaneous holders of a resources make for a more complex system
  - also makes thread-safety harder
  - performance cost related to the bookkeepig of the reference-counting
  
### `std::weak_ptr`
- hold a reference to a shared object along with other shared pointers but they don't increment the  reference count
- a weak pointer needs to check if the object it points to is still alive
  - copied into a shared pointer: `shared_ptr<int> sp = wp.lock()`

### `boost::scoped_ptr`
- it disables the copy and even the move construction. 
- it's the sole owner of a resource and its ownership cannot be transfered

### `auto_ptr`
- copy constructor = move constructor for unique_ptr
- shouldn't be used

## How to implement the pimpl idiom by using `unique_ptr`
"Pointer to implementation" is a widespread technique to cut compilation dependencies.

### The pimpl
- if a header `A.h` is included in `B.h`, then whenever `A.h` is modified, all the clients of `B` have to recompile, even if they don't use `A` directly
- pimpl idiom: add a level of indrection `BImpl` that takes on `A`
- `B` class contains a pointer to `BImpl`
- pointers only need a forward declaration to compile

### Using `unique_ptr` to manage the life cycle
- deleting a pointer leads to undefined behavior if
  - the pointer has type void*
  - the type pointed to is incomplete, i.e., only forward declared, just like our `BImpl` pointer
- the default destructor is declared inline in the header file `B.h`, where the the `BImpl` is incomplet
  - solution is to move the the declaration into `BImpl` after the class is defined, where `BImpl` is complete

## How to transfer `unique_ptr` from a set to another set
- two independent pointer: just do `std::move`

### Sets of unique pointers and polymorphic
If we do runtime inheritance via inheritance, we would want to hold a set of unique pointers to the base class : `unique_ptr<Base>` and if we don't want duplicates, we will do a set of unique pointers: `std::set<unqiue_ptr<Base>>`. However, this will call the comparison method of `std::unique_ptr`, which compares the memory addresses of the pointers inside them.
What we want is the elements of the unique_ptrs have no duplicates, not the unqiue_ptrs themselves. We can write comparison method ourselves and do `std::set<unique_ptr<Base>, myComparison>`.

### Transferring unique_ptrs between two sets
- if we just use `insert()`, we will have a compilation error
- the insert methods attempts to make a copy of the `unique_ptr` elements

#### C++17: `merge`
- the `merge` method makes the destination take over te nodes of the source and the unique_ptrs don't feel a thing

#### We can't move from a set
- even if we do `move`, some ptrs will also be copied which gives us the same compilation error as before
- what happens:
  - `move` dereferences the iterator on set and get a `const unique_ptr &`
  - calls `move` on the references, thus getting a `const unqiue_ptr&&`
  - it calls the `insert` method on the insert output iterator and passes it this `const unique_ptr&&`
  - the `insert` method has two overloads: one that takes `const unqiue_ptr&` and one takes a `unique_ptr&&`. Then the compiler cannot resolve the call to the second method because our poitner is constant and therefore calls the first one instead
- the key is that set's iterator returns constant element of the set

