## Accustoming yourself to C++
### Use `const` whenever possible
- `const iterator` vs `const_iterator`: for the first, the iterator itself is constant but can change the content pointed to; for the second, we can change the iterator but not the content pointed to
- `const` member function:How many batches are there in one epoch? Or more directly, 
  - which functions may modify an object and which may not
  - make it possible to work with const object
  - however, `const` function may change the content of a member pointer because it doens't change the pointer itself
  - logical constant: a const member function might modify some of the bits in the object on which it's invoked but only in ways that the clients cannot detect
  - use `mutable` to allow non-static data members from teh constraints of bitwise constness
- code replication from `const` member functions:
  - let the non-constant version use the constant version via casting

### Make sure that objects are initialized before they're used
- Make sure that all constructors initialize everything in the object
- Better to initialize everything via member initialization i.e. `T:T(): var1(), var2() {}`
- Data members that are `const` or are references must be initialized via initialization list
- Base classes are initialized before derived classes. 
- Within each class, data members are initialzied in the order in which they are declared.

Order of initialziation of non-local static objects defined in different translation units:
- non-local static objects: static objects declared at namespace scope, inside classes and at file scope
- translation unit: a single object file, basically a single source file
- if the initialization of a non-local static object in one translation unit uses another non-local static object in a different translation unit, the object it uses could be uninitialized
- `extern`: tells the compiler that a variable is declared in another source module. The linker then finds this actual declaration and sets up the `extern` variable to point to the correct location
- solution: move the non-local static variables into functions, effectively making it a local static variable, which is guaranteed to be initialized when call the function
- example: `T& getX() { static T x; return x;}`
- then we just use `getX()` to replace `x` itself
cout << left << " " << current << " " << right << endl;
cout << left << " " << current << " " << right << endl;tors
cout << left << " " << current << " " << right << endl;lls
cout << left << " " << current << " " << right << endl;ll declare cout << left << " " << current << " " << right << endl;signment cout << left << " " << current << " " << right << endl;旗袍
cout << left << " " << current << " " << right << endl;e.g. copy cout << left << " " << current << " " << right << endl;pile cout << left << " " << current << " " << right << endl; to are cout << left << " " << current << " " << right << endl;
cout << left << " " << current << " " << right << endl;
cout << left << " " << current << " " << right << endl;ted cout << left << " " << current << " " << right << endl;
- sometimes the idea of making a copy of a certain class makes little sense because any two objects of that class should be different.
- to prevent the compiler from generating a copy operator, you define one yourself but make it private so no one can access it. Error will show up at linking time
- to move the link-time error up to compile time, we can write a Uncopyable base class and derive from that class
- use private inheritance: all member functions of the base class will only be accessible within the derived class

### Declare destructors virtual in polymorphic base classes
`TimeKeeper *ptk = getTimeKeeper();`
`delete ptk;`
The object is beging deleted via a base class pointer and the base class has a non-virtual destructor. C++ explicitly specifies taht the results are undefined and what typically happens is the derived part of the object is never destroyed.
If the class does not contain virtual functions, that often indicates it 旗袍

### Prevent exceptions from leaving destructors
Having exceptions leaving destructors could cause multiply active exceptions (e.g. when destructing a vector of the class), which causes undefined behavior.
Different ways to avoid the trouble:
- Terminate the program when there's exception: `std::abort()`
- Swallow the exception: only make log entry that the call fails

Not good since the program has no way to react to the ocndition that led to `close` throwing an exception in the first place. We can add a boolean variable in the class.

- Destructors should never emit exceptions. If functions called in a destructor may throw, the destructor should catch any exceptions, then swallow them or terminate the program
- If class clients need to be able to react to exceptions thrown during an operation, the class should provide a regular function that performs the operation.

### Never all virtual functions during construction or destruction
- To construct derived class, a base class constructor is first called
- In the base class constructor, the base version of the virtual function is used
- During the base class construction of a derived class object, the type of the object is that of the base class
- The same reasoning applies during destruction
- We can put the code into a private nonvirtual function

### Have assignment operators return a reference to `*this`
- Have the assignment operator return a reference to `*this` will make the operator right associative
  - `a=(b=c)`

### Handle assignment to self in `operator=`
- Traditional way is to check for assignment to self via identity test:
  - `if (this == &rhs) return *this`
- Also need to make sure the assignment is exception safe. If exception is thrown during `new`, our object will hold a deleted pointer
- Use a buffer for the original pointer:
  - `A* buf = this.ptr; this.ptr = new A(*rhs.ptr); delete *buf;`
  - The idea is to delete right before return
- "Copy and swap": make a copy of rhs and then swap the copy into the current instance
  - `A* buf(rhs); swap(buf);`
  - Can also ask for passing by value as opposed by reference

### Copy all parts of an object
- Only two functions copy objects:
  - copy constructor
  - copy assignment operator
- Need to make sure all data are copied when adding a new data
- Inheritance: also need to make sure the data from the base class are copied
  - do this by invoke the corresponding base class function

## Resource Management
### Use objects to manage resources
- RAII: resource acquisition is initialization
  - Resources are acquired during construction and released during destruction
- Use `auto_ptr` to hold the created pointers
  - the destructor will call `delete` automatically
  - copy/assign `auto_ptr` sets them to null and the copying pointer assume sole ownership of the resource
- Resources are acquired and immediately turned over to resource-managing objects
- Resource-managing objects use their destructors to ensure that resources are released
- An alternative is a `shared_ptr`
- Both `auto_ptr` and `shared_ptr` use `delete` as opposed to `delete[]`. So using with dynamically allocated arrays is a bad idea

### Think carefully about copying behavior in resource-managing classes
- What should happen when an RAII object is copied?
- Prohibit copying
- Reference-count the udnerlying resource
  - we can define a "deleter" for the `shared_ptr`
- Copy the underlying resource
- Transfer ownership of the underlying resource
- Common RAII class disallows copying and performing reference counting but other behaviors are possible

### Provide access to raw resources in resource-managing class
- We need a way to convert an object of RAII into the raw resource it contains (`shared_ptr` into a raw pointer......)
- `shared_ptr.get()`, `*`, `->`
- Implicit conversion function. 
  - this might increase the chance of errors
  - `Class A = B`: we implicitly convert the class of `B` into the class of `A` but now the same resources are available from both
- Access may be via explicit conversion or implicit conversion. In general, explicit conversion is safer but implicit conversion is more convenient for clients

### Use the same form in corresponding uses of `new` and `delete`
- Does the pointer being deleted point to a single object or to an array of objects? 
  - `new` paired with `delete`
  - `new A[100]` paired with `delete []`
- Shouldn't use typedefs for array types

### Store `new`ed objects in smart pointers in standalone statements
`processWidget(shared_ptr<Widget>(new Widget), priority())`
- Three things before call the function `processWidget`:
  - Call `priority`
  - Execute `new Widget`
  - Call `shared_ptr` contructor
- The order of execution is not definite. The following can happen:
  - `new Widget`
  - `priority()`
  - `shared_ptr()`
  - if exeception happens in `priority()`, memory can leak
- Use separate statement to create the `Widget` and store in `shared_ptr` 

## Designs and Declarations
### Make interfaces easy to use correctly and hard to use incorrectly
- Define classes
- Predefine the set of all valid values
- Unless there's a good reason not to , have your types behave consistently with the built-in types.
- Having functions return a smart pointer in the first place
- A `shred_ptr` offers a constructor that takes in both the raw pointer and a deleter

### Treat class design as type design
- How should objects of your new type be created and destroyed
- How should object initialization differ from object assignment
- What does it mean for objects of the new type to be passed by value
  - copy constructor
- What are teh restrictions on legal values for the new type
- Does the new type fit into an inheritance graph
- What kind of type conversions are allowed for the new type
  - implicitly converted `T1` to `T2`: type conversion operator in `T1` (`operator T2`) or non-explicit constructor in `T2`
  - http://www.cplusplus.com/doc/tutorial/typecasting/
- What operators and functions make sense for the new type
- What standard functions should be disallowed
- What should have access to the members of the new type
  - public, protected, private
  - friends, nested classes
- What is the "undeclared interface" of the new type
- How general is the new class?

### Prefer pass-by-reference-to-`const` to pass-by-value
- This avoids copy and destructing
- Also avoids the slicing problem: when a derived class is called as a base class object, the base constructor is called

### Don't try to return a reference when you must return an object
- Should avoid passing references to objects taht don't exist
- On the stack: reference to a deleted object
- On the heap: chained operator will cause leak
- The right way is to return a new object instead of a reference
- Never return a pointer or reference to a local stack object, a reference to a heap-allocated object, or a poitner or reference to a local static object if there's a chance that more than one such object will be needed.

### Declare data members `private`
- Use member functions give flexibility over teh read-write access
- Encapsulating gives flexibility over different implemetations
- `protected` data members are not better: if we remove a `protected` data member, all code involves derived classes will be broken

### Prefer non-member non-friend functions to member functions
- Object-oriented principles: data should be as encapsulated as possible
- If something is encapsulated, it's hidden from view  and ti affords us teh flexibility to change thins in a way that affects only a limited number of clients
- Encapsulation can be measured by how many things can see the data member
- Member functions can see the `private` data members
- A non-member, non-friend function can't access teh data members directly
- We can have it in the same namespace as the class
- We can put all convenience functions in multiple header files but on e namespace

### Declare non-member functions when type conversions should apply to all parameters
- Parameters are eligible for implicit type conversion only if they are listed in the parameter list
- Therefore, by making `operator*` a non-member function, we can allow compilers to perform implicit type conversions on all arguments

### Consider support for a non-throwing `swap`
- `swap` is a mainstay of exception-safe programming and a common mechanism for coping with the possiblity of assignment to self

## Implementations
### Postpone variable definitions as long as possible
- Variable might be unused if an exception is thrown
- Initialize with data is better than default ctor + assignment
- Variable that's used in a for-loop:
  - Define outside the loop: 1 constructor, 1 destructor, n assignment
  - Define within the loop: n constructor, n destructor

### Minimize casting
- In C++, casting is a feature you want to approach with great respect
- Different castings:
  - `(T) expression`, `T(expression)`
  - `const_cast<T>(expression)`: cast away the constness of objects
  - `dynamic_cast<T>(expression)`: "safe downcasting", to determine whether an object is of a particular type in an inheritance hierarchy
  - `reinterpret_cast<T>(expression)`: low-level casts that yield implementation-dependent results, e.g. casting a pointer to an `int`
  - `static_cast<T>(expression)`: force implicit conversion
- `static_cast<Window>(*this).onResize()` does not call the function for the base class directly. The cast creates a new, temporary copy of the base class part of `*this`, then invokes `onResize` on the copy.
  - If the function modifies the current object, it will only happen on the copy instead of the original object
  - The solution is to eliminate the cast
- The need for `dynamic_cast` generally arises because you want to perform derived class operations on what you believe to be a derived class object but only with a pointer or reference to base. Two ways to avoid:
  - Use containers to store teh derived class directly
  - Define virtual functions in the base class
- Avoid cascasding `dynamic_casts`

### Avoid returning "handles" to object internals
- Return references only when expecting clients to modify the data
- If a `const` member function returns a pointer, client could use the returned reference to modify data member
- A data member is only as encapsulated as the most accessible function returning a reference to it
- Instead, return `const` references. But this can leave handles that refer to parts of objects that don't exist any longer

### Strive for exception-safe code

- Leak no resources
- Don't allow data structures to become corrupted

- Functions offering the basic guarantee: if an exception is thrown, everything in the program remains in a valid state.
- Functions offering the strong guarantee promise that if an exception is thrown, the state of the program is unchanged
- Functions offering the nothrow guarantee promise never to throw exceptions, because they always do what they promise to do

- General strategy for strong guarantee: copy and swap. This is usually implemented by putting all the per-object data from the "real" object into a separate implementation object, then giving the real object a pointer to its implementation object
- A function can usually offer a guarantee no stronger than the weakest guarantee of the functions it calls

### Understand the ins and outs of inlining

- Inline function: to replace each call of that function with its code body
- Inline can be implicit or explicit:
  - Implicit: define a function within a class definition
  - Explicit: use the keyword `inline` 
- Inline functions must typically be in header files because inlining is done during compilation
- Calls to an inline function may or may not be inlined, depending on how the calls are made
  - For instance, calls through a function pointer to the inline function may not be inlined

- Constructors and destructors are often worse candidates for inlining
  - An empty constructor or destructor may not be "empty". Compiler may have put something in it. e.g. `try/catch` 

### Minimize compilation dependencies between files

- We can hide the object implementation behind a pointer by dividing a class into two
  - One offering only an interface
  - The other  implementing that interface
- The main class contains as a data member nothing but a pointer to its implementation class
- Avoid using objects when object references and pointers will do
- Depend on class declarations instead of class definitions whenever you can. In particular, we nevver need a class definition to declare a function using that class, not even if the function passes or returns the class type by value.
- Provide separate header files for declarations and definitions
- Interface class: the purpose is to specify an interface for derived classes
  - No data members
  - No constructors
  - A virtual destructor
  - A set of pure virtual functions that specify the interface
  - Clients of interface classes must program in terms of pointers and references because it's not possible to instantiate classes containing pure virtual functions
  - Use a static factory function to create real objects

## Inheritance and Object-Oriented Design

### Make sure public inheritance models "is-a."

### Avoid hiding inherited names

- Name-hiding rules: hides global variables with same name
- Searching order:
  - local scope
  - derived class
  - base class
  - namespace
  - global scope
- Overloaded functions in the base class may not be inherited by the derived class because of name-hiding
- Override default C++ name-hiding:
  - `using Base::function;` 

### Differentiate between inheritance of interface and inheritance of implementation

- Public inheritance is composed of two separable parts:
  - inheritance of function interfaces
  - inheritance of function implementations
- Member function interfaces are always inherited
- Pure virtual function: have derived classes inherit a  function interface only
- Simple virtual function: have derived class inherit a function interface and a default implementation
- Separate a simple virtual function into a pure virtual interface and a `protected` default implementation
- Non-virtual function: have derived classes inherit a function interface as well as a mandatory implementation

### Consider alternatives to virtual functions

- The template method pattern via the non-virtual interface idiom
  - Same `public` interface that calls virtual `private` functions
  - We can have codes before/after the call and make sure the proper context is set up/cleaned up
- The strategy pattern via function pointers
  - Require a function to be passed in when constructing the object
  - However, this function won't have access to non-public data and therefore might affect encapsulation
- The strategy pattern via `tr1::function`
  - `tr1::function` can hold any callable object
- The "classic" strategy pattern

### Never redefine an inherited non-virtual function

- If a non-virtual function is redefined `*Base ptr->function` will be different
- Non-virtual functions are statically  bound, meaning that if we define a derived pointer as a base pointer, the function called will be the base function

### Never redefine a function's inherited default parameter value

- Default parameter values are statically bound, i.e. bound to the static type (the type declared to have in the program text)
- Dynamic type: the type of the object to which it currently refers
- The dynamic type of an pointer and change
- We can use the non-virtual interface idiom to get around: use a public non-virtual function to call a private virtual function. The default parameter is set in the non-virtual interface and therefore can't be modified

### Model "has-a" or "is-implemented-in-terms-of" through composition

- Composition: objects of one type contain objects of another type

- For instance, a "set" contains a "list" but is not a "list" because "list" can have duplicate elements

### Use private inheritance judiciously

- Compilers will generally not convert a derived class object into a base class object if the inheritance is private
- The members inherited from a private base class become private members of the derived class, even if they were protected or public in the base class
- Private inheritance means "is-implemented-in-terms-of", only to take advantage of some of the features in the base class
- Private inheritance vs composition: use composition whenever you can, use private inheritance whenever you must:
  - Must use private inheritance when protected members/virtual functions enter the picture
- Private inheritance can also enable the empty base optimization

### Use multiple inheritance judiciously

- It's possible to inherit the same name from more than one base class and this leads to ambiguity
  - To resolve the ambiguity, we must specify which base class's function to call
- We might have an inheritance hierarchy with more than one path between a base class and a derived class (deadly MI diamond)
  - Whether the data members in the base class to be replicated for each of the paths?
  - By default, C++ replicates
  - To get around this, we can make all classes that immediately inherit from it use virtual inheritance
- Multiple inheritance can combine public inheritance from an interface class with private inheritance from a class that helps with implementation

## Templates and Generic Programming

### Understand the two meanings of `typename`

- `template<class T>` vs `template<typename T>` : nothing
- Nested dependent names (e.g. `C::const_iterator`) can lead to parsing difficulties because `C::const_iterator` might not be a typename
- We need to tell C++ that `C::const_iterator` is a type by adding `typename C::const_iterator`
- Anytime you refer to a nested dependent type name in a template, you must immediately precede it by the word typename
- Exception: nested dependent typenames in a list of base classes or as a base class identifier in a member initialization list

### Know how to access names in templatized base classes

- Calling base class functions from a derived class won't compile if templatized
  - The template parameter won't be known until later (when the derived class is instantiated)
  - We can have totally templatized classes of the base class that doesn't have the function
- We have to disable C++'s "don't look in templatized base classes" behavior
  - Preface calls with `this`: `this->base_class_function()`
  - Employ a `using` declaration: `using BaseClass<T>::base_class_function()`
  - Explicitly specify the base function is in the base class: `BaseClass<T>::base_class_function()`

### Factor parameter-independent code out of templates

- Using template can lead to *code bloat*: binaries with replicated code. We can have source code that looks fit and trim but object code that's fat and flabby
- In non-template code, replication is explicit: you can see that there's a duplication between two functions or two classes
- In template code, replication is implicit: there's only one copy of the template source code
- Can use a base class with part of the parameters as `template`
- There's a trade-off in efficiency:
  - With more template variables, we have more compile-time constants which give chance of compile-time optimization
  - Having only one version of the function decreases the size of the executable
- Templates generate multiple classes and multiple functions, so any template code not dependent on a template parameter causes bloat
- Bloat due to non-type template parameters can often be eliminated by replacing template parameters with function parameters or class data members
- Bloat due to type parameters can be reduced by sharing implementations for instantiation types with identical binary representations

### Use member function templates to accept "all compatible types"

- One of the things that real pointers do well is support implicit conversions. Derived class pointers implicitly convert into base class pointers, pointers to `non-const` objects convert into pointers to `const` objects ......

- We have to program such conversions among smart pointers, we have to program them explicitly

- We need conversion between any two type parameters for the template

- We can do this by a constructor template:

  `template<typename U>`

  `SmartPtr(const SmartPtr<U>& other);`

- Type conversions among built-in pointer types are implicit and require no cast, so it's reasonable for smart pointers to emulate that behavior

- We can restrict the conversions only to those we want by taking advantage of raw pointer conversion

  `SmartPtr(const SmartPtr<U>& other): heldPtr(other.get()) {}`

  `T* get() const {return heldPtr;}`

  `private:`

  `T* heldPtr t;`

- This can also be used to support assignment
- The compiler may generate default copy constructors. Declaring a generalized copy constructor in a class doesn't keep compilers from generating their own copy constructor. So we can declare a copy constructor as private

### Use traits classes for information about types

- `andvance` moves a specified iterator a specified distance

- Five categories of iterators

  - Input iterators: only move forward, only one step at a time, read what they're pointing to only once
  - Output iterators: only move forward, only one step at a time, only write what they point to once
  - Forward iterators: only move forward, only one step at a time, read/write multiple times
  - Bidirectional iterators: add in ability to move in both directions
  - Random access iterators: add in the ability to do "iterator arithmetic", i.e. to jump forward/backward an arbitrary distance

- There's a "tag struct" in the standard library to identify each type: `struct input_iterator_tag {};`

- To implement the `advance` function, we can do things differently depending on whether the iterator is a random access iterator

- The traits information for a type must be external to the type.

  `template<typename IterT>`

  `struct iterator_traits`

- Any user-defined iterator type must contain a nested typedef named `iterator_category` that identifies the appropriate tag struct

  `typedef random_access_iterator_tag iterator_category`

- `iterator_traits` just parrots back the iterator class's nested typedef:

  `template<typename IterT>`

  `struct iterator_traits {typedef typename IterT::iterator_category}`

- The second part of the `iterator_traits` handles iterators that are pointers

  `template<typename T>`

  `struct iterator_traits<T*> {typedef random_access_iteartor_tag iterator_category}`

- Extract the trait information: 

  `if (typeid(typename std::iterator_traits<IterT>::iterator_category) == typeid(std::random_access_iterator_tag))`

- However, the `typename` is known at compilation while the if statement is only evaluated at runtime

- Overload is like if statement at compiling time: overload with extra parameter

  `void doAdvance(IterT& iter, DistT d, std::random_access_iterator_tag)`

  `void doAdvance(IterT& iter, DistT d, std::bidirectional_iterator_tag)`

- Then wrap with `advance` function:

  `void advance(IterT& iter, DistT d)`

  `{`

  ​	`doAdvance(iter, d, typename std::iterator_traits<IterT>::iterator_category())`

  `}`

- Traits classes make information about types available during compilation. They are implemented using templates and template specialization
- In conjunction with overloading, traits classes make it possible to perform compile-time `if/else` tests on types

### Be aware of template metaprogramming

- Template metaprogramming: process of writing template-based C++ programs that execute during compilation
  - It makes things easy that would otherwise be hard or impossible
  - Template metaprograms run at compile-time
- `if/else` statement via overloading
- `for` loops via recursion
  - use `enum` when you need the value of a class during compilation

- TMP can accomplish
  - Ensuring dimensional unit correctness
  - Optimizing matrix operations
  - Generating custom design pattern implementations



## Customizing `new` and `delete`

### Understand the behavior of the new-handler

- Before `new` throws an exception in response to an unsatisfiable request for memory, it calls a client-specifiable error-handling function called a `new-handler`

- `new-handler` is a typedef for a pointer to a function that takes and returns nothing

- `set_new_handler` is a function that takes and returns a `new_handler`

- We can have the following behavior for a `new_handler`:

  - Make more memory available
  - Install a different new-handler
  - Deinstall the new-handler
  - Throw an exception
  - Not return

- The class's operator `new` ensures that the class-specific new-handler is used in place of the global new-handler when memory for class objects is installed

  `public:`

  ​	`static std::new_handler set_new_handler(std::new_handler p) throw();`

  ​	`static void* operator new(std::size_t size) throw(std::bad_alloc);`

  `private:`

  ​	`static std::new_handler currentHandler;`

- `set_new_handler` allows you to specify a function to be called when memory allocation requests cannot be satisfied

- Nothrow `new` is of limited utility because it applies only to memory allocation; associated constructor calls may still throw exception

### Understand when it makes sense to replace `new` and `delete`

- There are three of the most common reasons:
  - To detect usage error
  - To improve efficiency
  - To collect usage statistics
  - To increase the speed of allocation and deallocation
  - To reduce the space overhead of a default memory management
  - To compensate for suboptimal alignment in the default allocator
  - To cluster related objects near one another
  - To obtain unconventional behavior

## Miscellany

### Pay attention to compiler warnings

- Take compiler warnings seriously, and strive to compile warning-free at the maximum warning level supported by the compilers
- Don't become dependent on compiler warnings, because different compilers warn about different things. Porting to a new compiler may eliminate warning messages you've come to rely on.