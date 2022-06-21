# C++ Notes

## 1. Features of C++11
Here're the most important features of C++11 compared to previous versions:
- lambda expression
- automatic type deduction and `decltype`
- uniform initialization syntax
- deleted and default function
- nullptr
- range-based for loop
- std::array
- smart pointers


### 1.1 Lambda Expression
A lambda expression lets you define functions locally, at the place of the call, thereby eliminating much of the tedium and security risks that function objects incur.

The lambda expression has the following form:
```C++
[capture](parameter)->return_type {
    block
};
```

It's widely used as comparator.

### 1.2 Automatic Type Deduction and `decltype`

You can use `auto` to declare objects without specifying their types. This is chiefly useful when the type of the object is verbose or it's automatically generated (as in templates).

C++11 offers a similar mechanism for capturing the type of an object or an expression. The new operator decltype takes an expression and "returns" its type:
```C++
auto cmp = [&](Node* a, Node* b) {
    return a.val > b.val;
}
priority_queue<Node*, vector<Node*>, decltype(cmp)> pq(cmp);
```

### 1.3 Uniform Initialization Syntax
- Previous initialization notation
    - parenthesized initialization
        ```c++
        std::string s("hello");
        int m=int(); //default initialization
        ```
    - `=` initialization
        ```c++
        std::string s = "hello";
        int m = 5;
        ```
    - POD aggregates
        ```c++
        int arr[4]={0,1,2,3};
        struct tm today={0};
        ```
    - constructors member initializer list
        ```c++
        struct S {
            int x;
            S(): x(0) {}
        };
        ```

C++11 cleans up this mess with a **uniform brace notation**:
```c++
class C {
    int a;
    int b;
    public:
    C(int i, int j);
};

C c {0,0}; // C++11 only. Equivalent to: C c(0,0);

int* a = new int[3] { 1, 2, 0 }; // C++11 only

class X {
  int a[4];
public:
  X() : a{1,2,3,4} {} // C++11, member array initializer
};
```

With respect to containers, we can initialize containers intuitively:
```c++
// C++11 container initializer
vector<string> vs={ "first", "second", "third"};

map singers = { {"Lady Gaga", "+1 (212) 555-7890"},
    {"Beyonce Knowles", "+1 (212) 555-0987"}};
```

Similarly, C++11 supports in-class initialization of data members.

### 1.4 Deleted and Default Functions
- default function
    - definition: `int A() = default;` instructs the compiler to generate the default implementation for the function. Widely used in default constructors and default destructors.
    - advantages
        - more efficient than mannual implementations
        - rid the programmer from the chore of defining those functions manually
- deleted function
    - definition: `int A() = deleted;` avoids the object copying.
    - C++ automatically declares a copy constructor and an assignment operator for the class. Deleted constructors are used to prevent copying.

### 1.5 nullptr
`nullptr` replaces the bug-prone `NULL` macro and the literal `0` that have been used as null pointer substitutes for many years.
- features
    - `nullptr` is strongly typed
    - `nullptr` is applicable to all pointer categories, including function pointers and pointers to members.

### 1.6 Delegating Constructor
In C++11 a constructor may call another constructor of the same class:
```c++
class M {//C++11 delegating constructors
    int x, y;
    char *p;
public:
    M(int v) : x(v), y(0), p(new char [MAX]) {} //#1 target
    M(): M(0) {cout<<"delegating ctor"<<endl;} //#2 delegating
};
```

### 1.7 Rvalue Reference
The primary reason for introducing rvalue reference is **move semantics**.

- definition of rvalue:
    -  In C++ an lvalue is something that points to a specific memory location. On the other hand, a rvalue is something that doesn't point anywhere. In general, rvalues are temporary and short lived, while lvalues live a longer life since they exist as variables. It's also fun to think of lvalues as containers and rvalues as things contained in the containers. Without a container, they would expire.
    - rvalue is an expiring value(i.e. xvalue: denotes an object whose resources can be reused), a temporary object or subobject or a value that is not associated with an object.

- rvalue/lvalue references which have been declared are all lvalues, since they have address.

- usage of `std::move()`
    - realize the move semantics, avoid redundant copies, s.t. improve efficiency (e.g. `vector::push()`)

### 1.8 Range-based for loop
Range-based for loop execute a for loop over a range. It used as a more readable equivalent to the traditional for loop operating over a range of values, such as all elements in a container.
```C++
for ( range_declaration : range_expression ) 
    loop_statement
```

### 1.9 std::array
`std::array` is a container that encapsulates fixed size arrays.

This container is an aggregate type with the same semantics as a struct holding a C-style array T[N] as its only non-static data member. Unlike a C-style array, it doesn't decay to `T*` automatically. As an aggregate type, it can be initialized with aggregate-initialization given at most N initializers that are convertible to T: `std::array<int, 3> a = {1,2,3};`.


## 2. Features of C++14
- auto and lambdas
- constexpr

### 2.1 auto and lambdas
- auto can be used in more places, like function return type, it will deduce the return type from the return statements instead of having to use the peculiar trailing return type syntax and decltype.
- A related improvement with lambdas is that they now support auto for parameters;

### 2.2 constexpr
A `constexpr`-declared function in C++11 is a function which can be executed at compile time to produce a value to be used where a constant expression is required, such as when instantiating a template with an integer argument. While C++11 constexpr functions could only contain a single expression, C++14 relaxes those restrictions by allowing conditional statements such as if and switch, and also allowing loops, including range-based for loops.

## 3. Features of C++17
- simplified nested namespace
- variable declaration in if and switch
- compile-time if constexpr
- structure bindings
- direct list initialization of enums


### 3.1 simplified nested namespace
Before C++17 you have to use this verbose syntax for declaring classes in nested namespaces, but C++17 has introduced a new feature: double colons, to introduce nested namespaces.

### 3.2 variable declaration in if and switch
In C++17, we can declare variables inside the if statements and the scope is within the if statement itself.

### 3.3 compile-time if constexpr
The normal if statement condition is executed at run time, so C++17 introduced this new if constexpr statement. The main difference is that if constexpr is evaluated at compile time. Using if constexpr, only the condition under if constexpr will be compiled.

### 3.4 structure bindings
- You can now initialize multiple variables with a return value with a really cool syntax, which makes the C++ syntax just like any modern programming language, without losing its performance and system programming benefits.
```C++
tuple<string,int> student("Sriram", 10);
auto [name, age] = student;
```

- It basically allows you to declare multiple variables that are initialized with values from pairs, generic tuples or from custom structures and these multiples variable declarations happens in single statements.
```C++
map<string, string> fav_lang{
    { "John", "Java" },
    { "Alex", "C++" },
    { "Peter", "Python" }
};
 
auto result
   = fav_lang.insert({ "Henry",
                       "Golang" });
```

### 3.5 direct list initialization of enums
In C++ 17 initialize initialization of enums using braces is allowed

## 4. Features of C++20

- 3-way comparisons
- Map contains
- Range-based for loop with initialization
- New identifiers ( import, module)
- Calendar and time zone library
- std::string functions
- Array bounded/unbounded
- std::to_array
- Likely and unlikely attributes
- C++ Concepts library

### 4.1 3-way comparisons
The spaceship operator looks like `<=>` and its official C++ name is the 3-way comparison operator. It is called so because it is used by comparing two objects, then comparing that result with 0:
```
(x <=> y) < 0 is true if x < y
(x <=> y) > 0 is true if x > y
(x <=> y) == 0 is true if x and y are equal/equivalent.
```

### 4.2 Map contains
It gives a much easier way to check if a key is present in the associative container(set or map) in C++20. It replaces the `find` built-in function.
```C++
std::map<Key, T, Compare, Allocator>::contains
std::set<T>::contains
```

### 4.3 Range-based for loop with initialization
in C++20, an init-statement is introduced for initializing the variables in the loop-scope. It allows us to initialize the container we wish to loop through in the range-declaration itself.
```C++
for (init-statement(optional) ; range_declaration : range_expression)  {}
```
### 4.4 New identifiers (import, export, module)
Modules help to divide large amounts of code into logical parts. Modules promise faster compile times, isolation of macros, and make header files redundant.

### 4.5 Calendar and time zone library
```C++
#include <chrono>
using namespace std::chrono

auto date1 = 2020y/sep/8;
auto date2 = 21d/oct/2018;
auto date3 = jan/27/2019;
```

### 4.6 std::string functions
- `ends_with("suffix")`: Checks if the string ends with the given suffix.
- `starts_with("prefix")` :Checks if the string view starts with the given prefix.

### 4.7 Array bounded/unbounded
Checks whether `A` is an array type of known/unknown bound.
```C++
template< class T >
inline constexpr bool is_bounded_array_v = is_bounded_array<T>::value;
```

### 4.8 std::to_array
It converts the given array/”array-like” object to an `std::array`. It creates an `std::array` from the one-dimensional built-in array `a`. The elements of the `std::array` is copy-initialized from the corresponding element of `a`. Copying or moving a multidimensional built-in array is not supported.

### 4.9 likely and unlikely attributes
It provides a hint to the Optimizer that the labeled statement is likely/unlikely to have its body executed. Both attributes allow giving the Optimizer a hint, whether the path of execution is more or less likely.

### 4.10 C++ Concepts Library
The concepts library provides definitions of fundamental library concepts that can be used to perform compile-time validation of template arguments and perform function dispatch based on properties of types. These concepts provide a base for educational reasoning in programs.

## 5. Differences betweeen C++ and Python

| Parameter | C++ | Python |
| :-------: | :-: | :---:  |
| Compilation | Compiled |	Interpreted |
| Usage	| Not easy to write code.	| Easier to write code. |
| Nature of language |	Statically typed	| Dynamically typed |
| Portability |	Not portable	 |Portable
| Garbage collection |	Does not support Garbage Collection. |	Supports Garbage Collection.
| Installation	| No difficulty	| Difficult to install
| Types| 	Data types bound to names checked at compile time. |	Bound to values, checked at runtime.
| Scope of the variables	| Limited within the loops or blocks.	 |Accessible outside the loops or blocks.
| Rapid prototyping	| Not possible	| Possible
| Functions	| Restrictions on the type of parameters or return value.	| No restrictions on the type of parameters or return value.
| Efficiency	| Difficult to maintain.	| Easier to maintain
| Syntax complexity	| Uses blocks and semicolons.	| No blocks or semicolons.
| Speed of execution |	Faster	| Slower
| Performance	| High performance |	Low performance
| Popularity	| More popular for embedded or enterprise applications.	| Most popular for machine learning.
| Simplicity and usability	| Difficult to learn and is used in a low-level application.	| Simple and is used for machine learning or web applications.
## 6. Differences between C++ and Rust

- Rust intro
    
    Rust is a programming language designed for performance and safety, especially safe concurrency. It's syntactically similar to C++, but can gurantee memory safety by using a borrow checker to validate references. Rust achieves memory safety without garbage collection, and reference counting is optional.

- Features of Rust
    - memory safety

        It does not permit null pointers, dangling pointers, or data races. Data values can be initialized only through a fixed set of forms, all of which require their inputs to be already initialized. To replicate pointers being either valid or `NULL`, such as in linked list or binary tree data structures, the Rust core library provides an `option` type, which can be used to test whether a pointer has `Some` value or `None`. Rust has added syntax to manage lifetimes, which are checked at compile time by the borrow checker. Unsafe code can subvert some of these restrictions using the unsafe keyword.
    - memory management

        Rust does not use automated garbage collection. Memory and other resources are managed through the ownership, with optional reference counting.
    - ownership

        Rust has an ownership system where all values have a unique owner, and the scope of the value is the same as the scope of the owner. Values can be passed by immutable reference, using &T, by mutable reference, using `&mut T`, or by value, using `T`. At all times, there can either be multiple immutable references or one mutable reference (an implicit readers–writer lock). The Rust compiler enforces these rules at compile time and also checks that all references are valid.

### Technical Comparison
- Garbage Collection (memory safety)
    - Recent updates to C++ has had new features like RAII (Resource Acquisition is Initialization) to get rid of manual memory management, but they do not resolve the core issues under the hood.

    - To solve this issue, Rust uses a system of **ownership**, which enforces and improves its memory safety across the board. Rust would know when the variable gets out of scope or its lifetime ends at compile time and thus insert the corresponding LLVM/assembly instructions to free the memory. Rust also allows some kind of garbage collection, like atomic reference counting though.

- Pointers
    > In computer science, a pointer is an object that stores a memory address. In other words, the address “points to” other data in the program. Smart pointers are data structures that have additional metadata and functionalities. Most high and low-level languages have some sort of pointer functionality.
    - C++ offers the types `std::shared_ptr` and `std::unique_ptr` that can be used like smart pointers. Rust has several smart pointers in its standard library, like the reference counting smart pointer type.

    

## 7. Garbage Collection Strategy
### 7.1 Tracing
Tracing garbage collection is the most common type of garbage collection.

The overall strategy consists of determining which objects should be garbage collected by tracing which objects are reachable by a chain of references from certain root objects, and considering the rest as garbage and collecting them.


### 7.2 Reference Counting
Reference counting garbage collection is where each object has a count of the number of references to it. Garbage is identified by having a reference count of zero. An object's reference count is incremented when a reference to it is created, and decremented when a reference is destroyed. When the count reaches zero, the object's memory is reclaimed.

### 7.3 RAII (C++)
In RAII, holding a resource is a class invariant, and is tied to object lifetime. Resource allocation (or acquisition) is done during object creation (specifically initialization), by the constructor, while resource deallocation (release) is done during object destruction (specifically finalization), by the destructor. In other words, resource acquisition must succeed for initialization to succeed. Thus the resource is guaranteed to be held between when initialization finishes and finalization starts (holding the resources is a class invariant), and to be held only when the object is alive. Thus if there are no object leaks, there are no resource leaks.

## 8. C++ Smart Pointer
When we use raw pointers, we need to take care of deallocation if they point to objects with heap-allocated resource.

A smart pointer is a class that wraps a 'raw' (or 'bare') C++ pointer, to manage the lifetime of the object being pointed to. There is no single smart pointer type, but all of them try to abstract a raw pointer in a practical way.

The idea of smart pointer is to take a class with a pointer, destructor and overloaded operators like * and ->. Since the destructor is automatically called when an object goes out of scope, the dynamically allocated memory would automatically be deleted (or reference count can be decremented). 

### 8.1 unique_ptr
Allows exactly one owner of the underlying pointer.

### 8.2 shared_ptr
Reference-counted smart pointer. Use when you want to assign one raw pointer to multiple owners, for example, when you return a copy of a pointer from a container but want to keep the original. The raw pointer is not deleted until all shared_ptr owners have gone out of scope or have otherwise given up ownership. The size is two pointers; one for the object and one for the shared control block that contains the reference count. 

### 8.3 weak_ptr
Special-case smart pointer for use in conjunction with shared_ptr. A weak_ptr provides access to an object that is owned by one or more shared_ptr instances, but does not participate in reference counting. Use when you want to observe an object, but do not require it to remain alive. Required in some cases to break circular references between shared_ptr instances. 


## 9. Parallelism and Concurrency
- **Concurrency** is when two or more tasks can start, run, and complete in overlapping time periods. It doesn't necessarily mean they'll ever both be running *at the same instant*. For example, multitasking on a single-core machine. Concurrency is a property of a program or system.

- **Parallelism** is when tasks literally run at the same time, e.g., on a multicore processor. Parallelism is the run-time behaviour of executing multiple tasks at the same time.

```C++
// thread example
#include <iostream>       // std::cout
#include <thread>         // std::thread
 
void foo() 
{
  // do stuff...
}

void bar(int x)
{
  // do stuff...
}

int main() 
{
  std::thread first (foo);     // spawn new thread that calls foo()
  std::thread second (bar,0);  // spawn new thread that calls bar(0)

  std::cout << "main, foo and bar now execute concurrently...\n";

  // synchronize threads:
  first.join();                // pauses until first finishes
  second.join();               // pauses until second finishes

  std::cout << "foo and bar completed.\n";

  return 0;
}
```
