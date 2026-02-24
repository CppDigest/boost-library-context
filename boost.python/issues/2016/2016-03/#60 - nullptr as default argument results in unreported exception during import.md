# #60 - nullptr as default argument results in unreported exception during import [Open]

> Username: mamoll  
> Created at: 2016-03-05 21:49:02 UTC  
> Updated at: 2021-03-19 11:22:31 UTC  
> Url: https://github.com/boostorg/python/issues/60  

The code at the bottom can be compiled like so:  
  
```  
clang++ -std=c++11 -I/opt/local/include -I/opt/local/Library/Frameworks/Python.framework/Versions/3.5/include/python3.5m  -shared -o foo.so nullptr_error.cpp -L/opt/local/Library/Frameworks/Python.framework/Versions/3.5/lib -lpython3.5 -L/opt/local/lib -lboost_python  
```  
  
When importing the resulting module in Python I see the following error:  
  
```  
>>> import foo  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
SystemError: initialization of foo raised unreported exception  
```  
  
If I replace `nullptr` with `NULL`, then there is no error. This is with Boost 1.59 installed via MacPorts on the latest version of OS X with the latest version of Xcode.  
  
``` c++  
#include <boost/python.hpp>  
  
struct Foo  
{  
    Foo() : foo_(1) {}  
    void setFoo(int * foo = nullptr) { foo_ = foo ? *foo : 1; }  
    int getFoo() { return foo_; }  
    int foo_;  
};  
  
BOOST_PYTHON_MODULE(foo)  
{  
    boost::python::class_<Foo>("Foo", boost::python::init<>())  
        .def("setFoo", &Foo::setFoo, ( boost::python::arg("foo")=nullptr))  
        .def("getFoo", &Foo::getFoo)  
        ;  
}  
```

---

## Comment 1

> Username: ofloveandhate  
> Created at: 2018-01-12 04:03:54 UTC  
> Url: https://github.com/boostorg/python/issues/60#issuecomment-357137611  

i am getting the same or a similar *unhelpful* error using python3 when i `import` if i add `bases<BaseT>` to a certain class in [my library](https://github.com/bertiniteam/b2)...  
  
`initialization of _pybertini raised unreported exception`  
  
literally all i add is a `bases` to my `class_`, and i cannot `import` my library any more.  this is with boost & boost.python versions 1.66; and Xcode `Version 8.0 (8A218a)`, with  
```  
clang version 4.0.0 (trunk 289544)  
Target: x86_64-apple-darwin15.6.0  
Thread model: posix  
```  
my python3 line to compile your example is   
```  
g++ -std=c++11 -I/usr/local/include -I/Users/ofloveandhate/anaconda/include/python3.6m  -shared -o foo.so nullptr_error.cpp -L/Users/ofloveandhate/anaconda/lib -lpython3.6m -L/usr/local/lib -lboost_python3-mt  
```  
with boost and boost.python installed from homebrew, and some anaconda install on the path.  
  
to check my sanity, i compiled your above test example, and get the same problem, 22 months later.  pretty sure it is the same problem as i am experiencing.  
  
if i compile my code with python2 installed at /usr/local/bin which resolves to version 2.7.13 installed from brew using this   
```  
g++ -std=c++11 -I/usr/local/include -I/usr/local/Cellar/python/2.7.13/Frameworks/Python.framework/Versions/2.7/include/python2.7  -shared -o foo.so nullptr_error.cpp -L/usr/local/opt/python/Frameworks/Python.framework/Versions/2.7/lib -lpython2.7 -L/usr/local/lib -lboost_python  
```  
i get the following much better error message:  
```  
RuntimeError: extension class wrapper for base class bertini::Observer<bertini::tracking::AMPTracker> has not been created yet  
```  
  
in fact, compiling your example using python2 yields this error:  
```  
TypeError: No to_python (by-value) converter found for C++ type: std::nullptr_t  
```  
  
why does python2 yield much better error reporting?    
is there a way to make the python3 error message more helpful?  what are the circumstances in which this particular error can be generated?

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-02-11 02:36:24 UTC  
> Url: https://github.com/boostorg/python/issues/60#issuecomment-364714240  

i again encountered this just now (feb 10 2018).  i got   
```  
initialization of _pybertini raised unreported exception  
```  
upon `import` of my "successfully" built library.  and the issue was that i had not yet exposed an `enum`.  would have been clearly reported by python2, not with python3.

---

## Comment 3

> Username: dfaure-kdab  
> Created at: 2021-03-19 11:21:58 UTC  
> Updated at: 2021-03-19 11:22:31 UTC  
> Url: https://github.com/boostorg/python/issues/60#issuecomment-802761374  

I think the lack of precise error reporting was fixed in https://github.com/boostorg/python/issues/280.  
But I'm not sure if the problem with nullptr was fixed.
