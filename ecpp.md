## Accustoming yourself to C++
### Use `const` whenever possible
- `const iterator` vs `const_iterator`: for the first, the iterator itself is constant but can change the content pointed to; for the second, we can change the iterator but not the content pointed to
- `const` member function:
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