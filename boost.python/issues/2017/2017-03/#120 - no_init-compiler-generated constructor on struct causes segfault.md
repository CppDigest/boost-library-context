# #120 - no_init/compiler-generated constructor on struct causes segfault [Open]

> Username: mg262  
> Created at: 2017-03-16 11:34:03 UTC  
> Updated at: 2017-03-16 18:05:52 UTC  
> Url: https://github.com/boostorg/python/issues/120  

If I compile the following extension, then as soon as I load it into a Python script I get a `Segmentation fault (core dumped)`. The cause seems to be the `no_init` on class Bar. I'd be grateful for any help.  
  
```  
#include <boost/python.hpp>  
  
using namespace std;  
using namespace boost::python;  
  
struct Bar {  
    string x;  
};  
  
BOOST_PYTHON_MODULE (libtest_python) {  
    class_<Bar>("Bar", no_init)  
            .def_readonly("x", &Bar::x);  
}  
```  
  
The reason for using the `no_init` is that `init<string>()` doesn't pick up the compiler-generated constructor. If I write my own constructor and use `init<string>()`, it loads fine.  (But I'm wrapping code that I can't modify, and the struct constructor is compiler generated.)  
  
Using Windows/Cygwin, gcc 5.4.0, python 2.7 and Boost 1.60.0, all installed via Cygwin rather than windows.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-03-16 12:14:29 UTC  
> Url: https://github.com/boostorg/python/issues/120#issuecomment-287039210  

I can't reproduce the segfault on my Linux laptop.  
However, I'd like to understand the `no_init` business: Code like  
```  
struct Bar {  
    string x;  
};  
```  
will let the compiler generate a default and a copy-constructor, so no `no_init` should be needed (and indeed, if I remove the `no_init` argument above, all works fine and I can construct a `Bar` instance in Python).   
As written, there is no constructor taking a `string` argument, so `init<string>()` would be wrong. In fact, as written, there is no (Python-visible) constructor at all, so I wonder how you expect to construct `Bar` instances (some factory function perhaps ?)

---

## Comment 2

> Username: mg262  
> Created at: 2017-03-16 12:43:25 UTC  
> Url: https://github.com/boostorg/python/issues/120#issuecomment-287045454  

I have worked around the problem by adding this:  
  
```  
template<typename T>  
T* dummyCLCConstructor() { // Needed to prevent segfaults from boost::python  
    throw runtime_error{"This code should not be executed"};  
}  
```  
  
and then   
  
`.def("__init__", make_constructor(dummyCLCConstructor<Bar>)) // Needed to prevent segfault`

---

## Comment 3

> Username: mg262  
> Created at: 2017-03-16 12:50:02 UTC  
> Updated at: 2017-03-16 12:51:37 UTC  
> Url: https://github.com/boostorg/python/issues/120#issuecomment-287046914  

Regarding the necessity of `no_init`... my actual use case is a bit more complicated than the minimal working example I posted. The code looks like this:  
  
  
  
    struct Foo {  
        double x;  
        double y;  
  
        Foo(double x, double y); //validates x and y  
    };  
  
    struct FooAndString {  
        Foo foo;  
        string s;  
    };  
  
and I want to expose `FooAndString`. `Foo` has no argument-less constructor, and consequently `FooAndString` has no argument-less constructor. Consequently if you leave out the `no_init`, you get an error message  
  
    error: use of deleted function ‘FooAndString::FooAndString()’  
  
But you can construct a `FooAndString` by writing  
  
    FooAndString{Foo{0.0,0.0},"s"}  
  
And this is what functions that return a `FooAndString` do. (It's basically a named pair type, where one element of the pair does not have a default constructor.)

---

## Comment 4

> Username: mg262  
> Created at: 2017-03-16 13:06:47 UTC  
> Url: https://github.com/boostorg/python/issues/120#issuecomment-287050790  

By the way, it's definitely the combination of `no_init` and `def_readonly` that causes the problem. `no_init` on its own doesn't cause segfaults.

---

## Comment 5

> Username: mg262  
> Created at: 2017-03-16 18:05:52 UTC  
> Url: https://github.com/boostorg/python/issues/120#issuecomment-287143445  

Oh, also  
  
>there is no (Python-visible) constructor at all  
  
objects of the type in q. are only ever created in C++ and sent to Python.
