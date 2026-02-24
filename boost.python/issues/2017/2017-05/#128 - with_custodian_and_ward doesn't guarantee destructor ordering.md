# #128 - with_custodian_and_ward doesn't guarantee destructor ordering [Open]

> Username: bmerry  
> Created at: 2017-05-19 13:12:54 UTC  
> Updated at: 2017-05-19 13:12:54 UTC  
> Url: https://github.com/boostorg/python/issues/128  

The weakref trick used by `with_custodian_and_ward` relies on the nurse object being cleared before it calls the callback which drops the reference to the patient. This is mostly but not always true: when the cyclic garbage collector runs, it first makes all weakref callbacks before freeing the garbage (at least in Python 2.7.12). This can lead to the patient's destructor running before the nurse's, with dire consequences if the nurse references the patient in its destructor.  
  
pybind11 uses the same weakref trick and suffers the same bug: https://github.com/pybind/pybind11/issues/856.  
  
## Example code  
  
keepalive.cpp:  
```c++  
#include <boost/python.hpp>  
#include <iostream>  
  
namespace py = boost::python;  
  
class B;  
  
class A {  
private:  
    B &b;  
public:  
    A(B &b) : b(b) {}  
    ~A() { std::cerr << "In A::~A()\n"; }  
};  
  
class B {  
public:  
    ~B() { std::cerr << "In B::~B()\n"; }  
};  
  
BOOST_PYTHON_MODULE(keepalive) {  
    py::class_<A>("A", py::init<B &>()[py::with_custodian_and_ward<1, 2>()]);  
    py::class_<B>("B");  
}  
```  
  
test.py:  
```python  
#!/usr/bin/env python  
from keepalive import A, B  
  
lst = [A(B())]  
lst.append(lst)  
del lst  
```  
  
Output:  
```  
In B::~B()  
In A::~A()  
```
