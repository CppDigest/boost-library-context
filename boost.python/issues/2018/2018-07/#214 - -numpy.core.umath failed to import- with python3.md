# #214 - "numpy.core.umath failed to import" with python3 [Closed]

> Username: uwydoc  
> Created at: 2018-07-01 01:37:53 UTC  
> Updated at: 2018-07-12 05:38:38 UTC  
> Closed at: 2018-07-11 20:40:21 UTC  
> Url: https://github.com/boostorg/python/issues/214  

i know #209 has been considered to be an arch-related build problem and thus marked as closed. however, i encountered a very similar import error, with no "endianness error" reported, just `ImportError. numpy.core.umath failed to import`.  
  
base code:  
```  
#include <boost/python.hpp>  
#include <boost/python/numpy.hpp>  
  
void hello() { PySys_WriteStdout("hello\n"); }  
  
BOOST_PYTHON_MODULE(hello) {  
  boost::python::numpy::initialize();  
  boost::python::def("hello", hello);  
}  
```  
  
CMakeLists.txt  
```  
find_package(PythonLibs 3)  
find_package(Boost COMPONENTS python3 numpy3)  
  
PYTHON_ADD_MODULE(hello hello.cpp)  
target_include_directories(hello PRIVATE ${PYTHON_INCLUDE_DIR})  
target_link_libraries(hello Boost::boost Boost::python3 Boost::numpy3 ${PYTHON_LIBRARIES})  
```  
  
my build env:  
```  
os: ubuntu 16.04 x86_64  
cc: clang 3.8.0 (NOTE: installed with 'apt install clang')  
python: 3.5.2 (NOTE: lib with 'm' suffix, installed with 'apt install python3 python3-dev')  
numpy: 1.14.5 (NOTE: installed with 'python3 -m pip install --user numpy')  
boost build cmd:  
  ./bootstrap.sh --with-toolset=clang --with-python=python3  
  ./b2 toolset=clang link=static cxxflags="-std=c++11 -fPIC" variant=release --with-python  
```  
  
after some tweaking, i found the following workarounds (modify `libs/python/src/numpy/numpy.cpp`)  
1. swap the order of `wrap_import_array()` and `import_ufunc()`  
2. **or** replace `wrap_import_array()` with `_import_array()`  
2. **or** add `return NULL;` after `import_array()` in `wrap_import_array` implementation for py3  
  
i googled around the web, with no similar result. wierd. hope this workaround would help someone else.  
  
---  
another interesting thing is, if i call `wrap_import_array` and `import_ufunc` directly in my own module .c file, as follows:  
```  
#include <boost/python.hpp>  
#include <numpy/arrayobject.h>  
#include <numpy/ufuncobject.h>  
  
static void * wrap_import_array() { import_array(); }  
  
void hello() { PySys_WriteStdout("hello\n"); }  
  
BOOST_PYTHON_MODULE(hello) {  
  wrap_import_array();  
  import_ufunc();  
  
  boost::python::def("hello", hello);  
}  
```  
program crashed with `Illegal instruction` message when `import hello`, stack trace contains no helpful message (i think); however, if i call `np::initialize()` instead, the program would not crash, but here comes the `ImportError. numpy.core.umath failed to import` error.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-07-05 04:29:51 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-402603388  

Many thanks, that's really helpful !  
What worries me, though, is that I'm still unable to reproduce the issue in my own builds / tests, so I feel like I would apply some patches blindly without either understanding them nor being able to truly verify that the change fixes the reported issue.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2018-07-08 21:06:02 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-403317197  

@uwydoc , it would be extremely helpful if you could provide some debugging output for the failing case above. (I'm unable to reproduce the problem on Fedora 28 and Ubuntu 16.04.)  
  
Could you run the failing program in `gdb` ? You would start a `gdb` session, call `target exec python` (or `target exec python3` as the case may be), then `run`, which gives you a python prompt from which you can call `import hello`. I really need to get to the bottom of this, i.e. understand why there is an illegal instruction depending on the initialization order. my suspicion is some memory corruption which just manifests itself in different ways depending on the initialization order.  
I may try to build a docker container that would allow me to debug this myself, but this may take some time. In particular, it's unlikely to happen before the current release... :-(  
  
Many thanks !

---

## Comment 3

> Username: uwydoc  
> Created at: 2018-07-10 05:31:50 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-403705245  

@stefanseefeld i did have the stack trace while debugging this issue, and IMO it is useless to just look at it and you probably need a live gdb session where you can inspect registers and dissamble some memory address to debug it. anyway, i would get the stack trace you need ASAP.

---

## Comment 4

> Username: uwydoc  
> Created at: 2018-07-10 05:51:18 UTC  
> Updated at: 2018-07-10 05:55:36 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-403708368  

@stefanseefeld as i am working in a restricted env, i can only post screenshot of the stack trace. see attachements for details. i post info stack, registers, frames, and disassemble the break address. hope it will help.  
![boost python 2134 part1](https://user-images.githubusercontent.com/2803613/42491655-46bf965c-8448-11e8-9a31-fa9676d89581.png)  
![boost python 2134 part2](https://user-images.githubusercontent.com/2803613/42491657-4afac188-8448-11e8-8882-dd1c56a0bad9.png)  
![boost python 2134 part3](https://user-images.githubusercontent.com/2803613/42491659-4cee8e98-8448-11e8-843f-60a86aa259f1.png)  
  
btw, test codes:  
```  
#include <boost/python.hpp>  
#include <numpy/arrayobject.h>  
#include <numpy/ufuncobject.h>  
  
static void * wrap_import_array() { import_array(); }  
  
void hello() { PySys_WriteStdout("hello\n"); }  
  
BOOST_PYTHON_MODULE(hello) {  
  wrap_import_array();  
  import_ufunc();  
  
  boost::python::def("hello", hello);  
}  
```

---

## Comment 5

> Username: uwydoc  
> Created at: 2018-07-10 06:04:53 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-403710627  

@stefanseefeld  sorry, previous reply is the stack trace for a default build. i should upload the stack trace for a debug build. so here is the stack trace for a debug build.  
![boost python 214 debug part1](https://user-images.githubusercontent.com/2803613/42492073-2db489ea-844a-11e8-8cac-c5afd7a814b1.png)  
![boost python 214 debug part2](https://user-images.githubusercontent.com/2803613/42492074-3007b85c-844a-11e8-9bc4-958f40f2601f.png)  
![boost python 214 debug part3](https://user-images.githubusercontent.com/2803613/42492078-32846792-844a-11e8-8105-3cb460b5075a.png)

---

## Comment 6

> Username: WanzenBug  
> Created at: 2018-07-11 08:16:59 UTC  
> Updated at: 2018-07-11 19:03:01 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-404084828  

I can reproduce this error on fedora 28 (with the boost package maintained in the repository). It only happens with python3, and only if boost was compiled with at least "-O". I recompiled boost with UBSan and consistently get:  
  
```  
libs/python/src/numpy/numpy.cpp:19:15: runtime error: execution reached the end of a value-returning function without returning a value  
```  
As I understand it, `wrap_import_array` is missing a `return NULL;` in python3 (for python2 the signature is just `void`, so no problem there). Since calling a function with return type other than void without hitting a `return x;` statement is undefined behavior, the compiler is free to assume it does not happen. The only other way is for `_import_array` to return a value < 0 and one way to achieve this is by failing the endian check.  
  
Why exactly the endianness check is not fully clear to me. I'm still not sure if access to a non-active member of a union is not UB. UBSan does not complain so I'm not sure. In any case, that is a problem of numpy and not boost.python

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2018-07-11 20:30:16 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-404299881  

You shouldn't actually be calling  
```  
wrap_import_array();  
  import_ufunc();  
```  
yourself. Rather, you should call `boost::python::numpy::initialize()`. Do you still get a segfault if you call that instead ?

---

## Comment 8

> Username: WanzenBug  
> Created at: 2018-07-11 20:38:29 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-404301899  

Yes, this is how I first stumbled on this issue. The very first snippet in in this issue triggers the "detected other endianness" bug.

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2018-07-11 20:39:52 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-404302220  

OK, thanks ! So I'm merging this PR, and then will immediately propagate it to master, so it will make it into the release. Many thanks for this !

---

## Comment 10

> Username: uwydoc  
> Created at: 2018-07-12 05:32:55 UTC  
> Updated at: 2018-07-12 05:38:38 UTC  
> Url: https://github.com/boostorg/python/issues/214#issuecomment-404394378  

@WanzenBug thanks for you detailed explaination, it dispersed my previous confusion.  
however, i do not think it is a 'problem' of numpy, but rather a design decision. accordingly to numpy's own test/demo code([here](https://github.com/numpy/numpy/blob/master/numpy/core/src/umath/_umath_tests.c.src#L507)), the macro `import_array` and `import_ufunc` are meant to be used directly in the top-level module function, that is, `initxxx` or `PyInit_xxx`, not to be wrapped in another function, for example, `boost::numpy::initialize`/`wrap_import_array` in our scenario. with python2, `initxxx` requires no return value, so the macro `import_array` returns nothing in the if branch, with python3, `PyInit_xxx` requires a return type of `PyObject*`, so `import_array` returns `NULL`, pretty reasonable.  
``` c  
// https://github.com/numpy/numpy/blob/v1.14.5/numpy/core/code_generators/generate_numpy_api.py#L130  
#if PY_VERSION_HEX >= 0x03000000  
#define NUMPY_IMPORT_ARRAY_RETVAL NULL  
#else  
#define NUMPY_IMPORT_ARRAY_RETVAL  
#endif  
  
#define import_array() {if (_import_array() < 0) {PyErr_Print(); PyErr_SetString(PyExc_ImportError, "numpy.core.multiarray failed to import"); return NUMPY_IMPORT_ARRAY_RETVAL; } }  
```  
as a user of the numpy api, i think we should respect its design decision. so it is a issue of boost.python itself.  
  
btw, before the fix, building boost.python with clang always issues a warning complaining 'not all path return a type' in `wrap_import_array`, so i think we should treat compiler warnings more seriously. after all, boost.python is part of **THE** Boost library, right?
