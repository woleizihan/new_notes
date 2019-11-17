# C++ Notes

## Sequential Container
### Vector vs List
Vector
- erase: all iterators that refer to the element erased or subsequent elements are invalidated because all subsequent elements are moved
- push_back: all iterators are invalidated because the entire vector might be moved to get more room
- insert: move all the subsequent elements to insert, so O(#inserted + #moved)

List
- erase: only invalidate the iterator refers to the element erased
- sort: we can't use std::sort but we can use list.sort() member function
- insert: only affect the consecutive elements, so #inserted complexity

### Generic function
- template<typedef T>
- Pass by reference T& to accomodate different data structure with a common interface

## Library algorithm
- copy(begin, end, out): can use back_inserter(vector)
- find_if(start, end, boolfunction)
- equal(first sequence start, first sequence end, second sequence start)
- find(start, end, value)
- transform(start, end, out, function)
- remove_copy_if(start, end, out, bool_function)
- remove_if(start, end, bool_function)
- stable_partition(start, end, bool_function): takes a sequence and rearranges its elements so that ones that satisfy a predicate precede the ones that do not satisfy it
- Algorithms only change the elements not the container

## Associative Containers
Hash table
- For each key type, there must be a hash function, which computes an appropriate integer value form the value of the key
- Performance sensitive to the details of the hash function
- Not ordered
- The key type needs only the $<$ operator or equivalent comparison function

Map
- Elements are kept sorted by key

## Generic Functions
- Traditional edit-compile-link model: instantiation often happens not at compile time but at link time
- In order to instantiate a template, the definition of the template, not just the declaration has to be accessible to the implementation
- Take iterator arguments instead of container argument: can run independent of the container

Iterator categories
- Sequential read-only access: read elements sequentially
  - find
- Sequential write-only access
  - copy
- Sequentail read-write access
  - replace
- Reversible access: both ++ and --
  - reverse
- Random access
  - binary_search: indexing

Why one pass the end? 
- If empty then no last element at all
- We can compare iterators only for equality and inequality, no need for less
- Indicate "out of range": return the out of range iterator to indicate failure

## Managing memory and low-level data structures
### Function pointer
- any use of a function that is not a call is assumed to be taken its address, even without the explicit &
- however, if we want to return a function pointer, we have to explicitly say it

### Arrays
- arrays can't grow or shrink dynamically the way library containers do becuase the number of elements must be known at compile time
- Whenever we use the name of an array as a value, that name represents a pointer to the initial element of the array
- Pointers are random-access iterators for arrays. If p is the pointer to m-th element then p[n] is the pointer to (m+n)-th element

### Arguments to main
- argc: number of arguments
- argv: first is the .out file, and any after is the argument seperated by space

### Three kinds of memory management
- Automatic: a local variable occupies memory that the system allocates when it encounters the variable's definition during execution. The system automatically deallocates taht memory at the end of the block that contains the definition.
- Statically: use static keyword on a variable. We want to allocate it once and only once. And we don't want to deallocate it as long as our program runs. We can return the address of a static variable
  - However, every pointer_to_static call will return a pointer to the same object
- Dynamic allocation: use new/delete keywords
  - delete only works for pointer that was allocated by new or be equal to zero 

## Abstract data types
- explicit constructor: the compiler will use the constructor only in the contexts in which the user expressly invokes the constructor
  - Vec<int> vi(100): explicit
  - Vec<int> vi = 100: implicit
- Use typedef to hide the implementation details
- const member function: not allowed to change the object itself
  - use const version for read only and non-const version for read and write
  - we can omit the type parameters when we are within the scope of the template
  - v=split(line): two step process
    - on entry: initialization of split's parameter from line
    - on exit: initialization of the return value by copy
    - on exit: assignment to v
- rule of three: if the class needs a destructor, it probably needs a copy construcotr and an assignment operator too

Flexible memory management
- new uses the default constructor to initialize each slot
- <memory> header, allocator<T>: allocates a block of uninitialized memory that is intended to contain objects of type T and returns a pointer to the initial element of that memory
  - T* allocate(size_t)
  - void deallocate(T*, size_t)
  - void construct(T*, const T&)
  - void destroy(T*)
  - uninitialized_fill(Out, Out, const T&): target range raw storage
  - Out uninitialized_copy(In, In, Out): target range raw storage

## Making class objects act like values
- friend function: add in the class to give access to private members
- explicit constructor: constructors that define the structure of the object being constructed, rather than its content
- conversion operator: operator double()
  - implicit conversion: feed directly into the place of another type
  - explicit conversion: call a member function to convert
- standard string library: get array of char from string
  - c_str(): copies the contents of the string inot a null-terminated char array. The class owns the array and the user is expected not to delete teh pointer. The array will be valid only until the next call of a member function that might change the string
  - data(): similar to c_str() but not null-terminated
  - copy(int): copies as many chars as indicated into space pointed to by the char*, which space the user must allocate and free

## Dynamic binding
- run-time selection of the virtual funciton to execute is relevant only when the function is called through a reference or a pointer. If it's called from an object, then we know the exact type of the object at compile time
- objects: statically bound, functions found at compile time
- pointer and reference: dynamically bound, functions may be found at run time
- C++ supports polymorphism through the dynamic-binding properties of virtual functions
  - pointer and reference functions are bound at run time
- virutal destructor: whenever we use polymorphism and pass in a pointer, we potentially need different destructors for each derived class

## Managing memory (almost) automatically
Problems with pointers:
- Copying a pointer does not copy the corresponding object
- Destroying a pointer does not destry its object, leading to memory leaks
- Deleting an object without destroying a pointer to it leads to a dangling pointer, which caused undefined behavior if used
- Create a pointer without initializing it leaves the pointer unbounded

Different handles
- shared: additional pointer, points to the count of uses
- make_unique: when a call to the pointer could result in modification, we need to make a new copy so the modification happens on the new copy and doesn't affect other shared pointers

Copying types that we can't control
-  make_unique requires a clone() method, which may not exist for all classes
-  fundamental theorem of software engineering: All problems can be solved by introducing an extra level of indirection
-  define intermediary global function that we can both call and create
-  we call the non-member clone() function and can overwrite it for classes without member function clone() 
-  template<>: template specialization, which defines a particular version of a template function for the specific argument type
