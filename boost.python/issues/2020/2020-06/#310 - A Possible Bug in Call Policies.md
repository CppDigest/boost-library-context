# #310 - A Possible Bug in Call Policies [Open]

> Username: hokiedsp  
> Created at: 2020-06-07 16:46:14 UTC  
> Updated at: 2020-06-07 16:46:14 UTC  
> Url: https://github.com/boostorg/python/issues/310  

Hi, I believe I just encountered a possible bug in boost-python...  
  
First of all, the back story, I'm putting together a python-boost based example for my [`cmaketools`](https://pypi.org/project/cmaketools/0.1.3/) project, which makes a cmake-based python package build as straight-forward as possible. I've taken the examples by [TNG](https://github.com/TNG/boost-python-examples) (who created his examples based off of the official documentation) and turned them into a package with modules with pytest tests. The modules based on his "09-Overloading" example is failing "08-CallPolicies" example in pytest *only when they are run together*.  
  
## Description  
  
When there is a class with the same name but under different a module (in a same package?), `return_value_policy<manage_new_object>()` call (possibly?) picks a wrong class to instantiate under pytest running 2 test py files each testing one of the classes.  
  
What is peculiar is that it works when only one test file is tested but fails when both are tested together. So, it could be a pytest issue (or Python's for that matter)...  
  
## Versions  
  
Boost: 1.71 (official pre-built binary)  
Boost Python DLL: boost_python37-vc142-mt-x64-1_71.dll  
Python: 3.7.6 (conda)  
Compiler: MSVC 2019 (16.6.1)  
OS: Window 10  
  
## (Near) Minimal Example  
  
Please find the entire example project at [https://github.com/hokiedsp/pytest_isinstance_issue](https://github.com/hokiedsp/pytest_isinstance_issue). Clone and run the following to reproduce (hopefully)  
  
```bash  
pip -e .  
pytest tests/test_callpolicies.py # passes  
pytest tests/test_nor.py # passes  
pytest  
```  
  
Last pytest call fails with the assertion error:  
  
```  
    def test_callpolicies():  
        f = policies.Example.factory()  
>       assert isinstance(f, policies.Example) and str(f) == "factory"  
E       AssertionError: assert (False)  
E        +  where False = isinstance(<boost_python_example.overloading.Example object at 0x00000202D58B0300>, <class 'boost_python_example.callpolicies.Example'>)  
E        +    where <class 'boost_python_example.callpolicies.Example'> = policies.Example  
```  
  
I'm anticipating `f` to be `boost_python_example.callpolicies.Example` but instead `boost_python_example.overloading.Example`.  
  
Side Note: If CMake cannot find Boost, you need to modify Line 14 of `setup.py`  
  
```python  
configure_opts = ['-DBOOST_ROOT=C:\\Users\\tikum\\AppData\\Local\\Programs\\boost_1_71_0']  
```  
  
## code snippets  
  
Here are the sections of most relevant:  
  
`callpolicies.cpp`  
  
```c++  
#include <sstream>  
#include <string>  
  
class Example {  
    Example(std::string n) : mName(n) {}  
    std::string mName;  
public:  
    std::string name() const { return mName; }  
    static Example* factory()  
    {  
        return new Example("factory");  
    }  
// ... snip...  
};  
  
#include <boost/python.hpp>  
using namespace boost::python;  
  
BOOST_PYTHON_MODULE(callpolicies)  
{  
    class_<Example>("Example", no_init)  
        .def("__str__", &Example::name)  
        .def("factory", &Example::factory,  
            return_value_policy<manage_new_object>())  
        .staticmethod("factory")  
    ;  
}	  
```  
  
`overloading.cpp`  
  
```c++  
#include <sstream>  
#include <string>  
  
class Example {  
public:  
    Example()  
    {}  
    // ... snip...  
};  
  
#include <boost/python.hpp>  
using namespace boost::python;  
  
BOOST_PYTHON_MEMBER_FUNCTION_OVERLOADS(makeIt_overloads, makeIt, 1, 3)  
BOOST_PYTHON_MODULE(overloading)  
{  
    void        (Example::*d1)()             = &Example::doit;  
    std::string (Example::*d2)(unsigned int) = &Example::doit;  
    void        (Example::*d3)(std::string)  = &Example::doit;  
  
    class_<Example>("Example")  
        .def("__str__", &Example::print)  
// ...snip...  
    ;   
}	  
```  
  
`test_callpolicies.py`  
  
```python  
#!/usr/bin/env python  
import pytest  
from boost_python_example import callpolicies as policies  
  
def test_callpolicies():  
    # e = callpolicies.Example() # won't work, constructore not available from python  
    f = policies.Example.factory()  
    assert isinstance(f, policies.Example) and str(f) == "factory"  
```
