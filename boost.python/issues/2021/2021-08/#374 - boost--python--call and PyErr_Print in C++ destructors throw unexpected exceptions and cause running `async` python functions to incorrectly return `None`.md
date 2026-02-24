# #374 - boost::python::call and PyErr_Print in C++ destructors throw unexpected exceptions and cause running `async` python functions to incorrectly return `None` [Open]

> Username: zbentley  
> Created at: 2021-08-27 22:32:08 UTC  
> Updated at: 2021-08-28 15:17:37 UTC  
> Url: https://github.com/boostorg/python/issues/374  

# Summary  
  
While inside a Python `async` function, if a C++ object's destructor calls any Python function via `boost::python::call` or `call_method`, an exception is raised (and Python code is not successfully called) if destruction is performed after Python's `return`.   
  
Even worse, calls to `PyErr_Print` in such destructors corrupt the Python interpreter, causing the current `async` function to return `None` regardless of its expected behavior.  
  
The outcome is that **certain boost::python calls in C++ destructors fail unexpectedly, and cause arbitrary `async` python functions to return `None` when they cannot logically do so**.  
  
This is very surprising and bad.  
  
# Steps to Reproduce  
  
1. Save the native code at the bottom of this issue as `native.cpp`.  
2. Save the Python file as `main.py`.  
3. Build the native library. I used this build script in Python:  
```python  
ffrom distutils.core import setup  
from distutils.extension import Extension  
native_ext = Extension('native', sources=['native.cpp'], libraries=['boost_python39-mt'])  
setup(name='repro', version='0', ext_modules=[native_ext])  
```  
2. Run `python main.py`  
3. Observe that the return value from `++++ Test 1: autodestruct` is `None`, despite the fact that **there is no way for that function to return None**.  
4. Observe that the None return is preceeded by a stacktrace with a `StopIteration` exception raised from a call to `doprint`, despite the fact that **there is no way for that function to raise StopIteration` as it contains no iterators**.   
5. Observe that the return value from `++++ Test 4: autodestruct with print` is similarly `None`, impossibly.  
6. Observe that the stacktrace emitted for test 4 is different from the trace in test 1 (and conceals the real interpreter invariant violation: `SystemError: _PyEval_EvalFrameDefault returned a result with an error set`, despite the fact that the error still occurs).  
7. Observe that the code in the destructor-called function for test 4 is never called ("called from native code" is never printed).  
  
# Expected behavior  
  
- All tests in `main.py` observe a numeric return value from the test functions.  
- In python `async` functions, `boost::python::call` or `call_method` calls that are performed in C++ destructors should behave equivalently to equivalent pure-python calls contained in `__del__` methods. Specifically, they should not raise errors, and should **especially** not corrupt interpreter state such that return values from unrelated functions are changed.  
  
# Specific issues  
  
There are three specific sub-issues here, ordered from most to least severe:  
1. Calls to `PyErr_Print` in the wrong place can cause unrelated code to return incorrect values.  
2. Calls from C++ to Python in C++ destructors can incorrectly raise exceptions, potentially interrupting cleanup logic.  
3. The "real" Python error (`SystemError: _PyEval_EvalFrameDefault returned a result with an error set`) is not emitted or otherwise observable from destructor-called Python functions in most cases, making diagnosis/error googling difficult.  
  
Removing calls to `PyErr_Print` prevents return-value corruption but issues 2 and 3 remain so long as Python code is invoked from C++ in a destructor.  
  
# Code to reproduce issue:  
  
Native code:  
```c++  
#include <boost/python.hpp>  
#include <iostream>  
#include <string>  
  
class Container  
{  
    PyObject* _inner;  
public:  
    Container(PyObject* inner) {  
        _inner = inner;  
        Py_XINCREF(inner);  
    }  
  
    virtual ~Container() {  
        std::cout << "Destroying native object" << std::endl;  
        try {  
            boost::python::call<void>(_inner);  
        } catch (boost::python::error_already_set e) {  
            std::cout << "Native destruction error: ";  
            PyErr_Print();  
        }  
        Py_XDECREF(_inner);  
    }  
};  
  
BOOST_PYTHON_MODULE(native)  
{  
    using namespace boost::python;  
    class_< Container >("Container", init<PyObject*>());  
}  
```  
  
Python:  
  
```python  
import asyncio  
import native  
  
class Container:  
    def __init__(self, inner):  
        self._inner = inner  
  
    def __del__(self):  
        self._inner()  
  
def empty():  
    pass  
  
def doprint():  
    print("Called from native code")  
  
async def test_autodestruct_control_group(num):  
    obj = Container(empty)  
    print("Returning", num)  
    return num  
  
async def test_autodestruct(num):  
    obj = native.Container(empty)  
    print("Returning", num)  
    return num  
  
async def test_autodestruct_print(num):  
    obj = native.Container(doprint)  
    print("Returning", num)  
    return num  
  
async def test_manual_destruct(num):  
    obj = native.Container(empty)  
    del obj  
    print("Returning", num)  
    return num  
  
async def test_manual_destruct_finally(num):  
    obj = native.Container(empty)  
    try:  
        print("Returning", num)  
        return num  
    finally:  
        del obj  
  
async def main():  
    print("++++ Starting tests")  
    print("++++ Test 0: control group (pure python)", await test_autodestruct_control_group(0))  
    print("++++ Test 1: autodestruct",await test_autodestruct(1))  
    print("++++ Test 2: manual destruct", await test_manual_destruct(2))  
    print("++++ Test 3: manual destruct in finally", await test_manual_destruct_finally(3))  
    print("++++ Test 4: autodestruct with print", await test_autodestruct_print(4))  
  
if __name__ == '__main__':  
    asyncio.run(main())  
```  
# Example (issue exhibiting) output:  
```  
∴ python main.py  
++++ Starting tests  
Returning 0  
++++ Test 0: control group (pure python) 0  
Returning 1  
Destroying native object  
Native destruction errors: StopIteration: 1  
  
The above exception was the direct cause of the following exception:  
  
SystemError: _PyEval_EvalFrameDefault returned a result with an error set  
++++ Test 1: autodestruct None  
Destroying native object  
Returning 2  
++++ Test 2: manual destruct 2  
Returning 3  
Destroying native object  
++++ Test 3: manual destruct in finally 3  
Returning 4  
Destroying native object  
Native destruction errors: Traceback (most recent call last):  
  File "/Users/zac.bentley/Desktop/Projects/pycpp_repro/main.py", line 18, in doprint  
    print("Called from native code")  
StopIteration: 4  
++++ Test 4: autodestruct with print  
```  
  
# Testing environment:  
OS: MacOS 11.5.2  
Architecture: x86_64  
Python interpreter: `Python 3.9.6` via Homebrew  
Boost version: `1.76.0` via Homebrew  
Boost-python version: `1.76.0` via Homebrew (`boost-python3`)  
Compiler:  
```  
clang++ --version  
Apple clang version 12.0.5 (clang-1205.0.22.9)  
Target: x86_64-apple-darwin20.6.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
```
