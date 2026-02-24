# #221 - Make boost compatible with python limited API [Open]

> Username: Raincloud7  
> Created at: 2018-07-26 07:51:47 UTC  
> Updated at: 2021-09-04 19:25:58 UTC  
> Url: https://github.com/boostorg/python/issues/221  

Currently boost python cannot be compiled with limited python API. To support many python versions it would be helpful to have this option.   
  
[https://docs.python.org/3/c-api/stable.html](https://docs.python.org/3/c-api/stable.html)  
[http://code.activestate.com/lists/python-cplusplus-sig/17408/](http://code.activestate.com/lists/python-cplusplus-sig/17408/)  
[https://www.python.org/dev/peps/pep-0384/](https://www.python.org/dev/peps/pep-0384/)

---

## Comment 1

> Username: cas--  
> Created at: 2019-06-27 09:13:59 UTC  
> Url: https://github.com/boostorg/python/issues/221#issuecomment-506264459  

To help with searching, specifically ability to define  `Py_LIMITED_API`

---

## Comment 2

> Username: cas--  
> Created at: 2019-06-27 09:38:42 UTC  
> Updated at: 2019-06-27 09:40:33 UTC  
> Url: https://github.com/boostorg/python/issues/221#issuecomment-506273561  

I have made a start on this and replace the methods in #270  which builds and passes tests.  
  
However defining  `Py_LIMITED_API` results in the build errors below:  
```diff  
--- include/boost/python/detail/wrap_python.hpp  
+++ include/boost/python/detail/wrap_python.hpp  
@@ -175,6 +175,7 @@ typedef int pid_t;  
 #if PY_MAJOR_VERSION == 2 && PY_MINOR_VERSION == 2 && PY_MICRO_VERSION < 2  
 # include <boost/python/detail/python22_fixed.h>  
 #else  
+#define Py_LIMITED_API  
 # include <Python.h>  
 #endif  
```  
  
Snippet of build error:  
```  
gxx.compile src.list.o  
In file included from include/boost/python/object/make_ptr_instance.hpp:8:0,  
                 from include/boost/python/to_python_indirect.hpp:11,  
                 from include/boost/python/converter/arg_to_python.hpp:10,  
                 from include/boost/python/call.hpp:15,  
                 from include/boost/python/object_core.hpp:14,  
                 from include/boost/python/object.hpp:9,  
                 from include/boost/python/list.hpp:10,  
                 from ./src/list.cpp:5:  
include/boost/python/object/make_instance.hpp: In static member function ‘static PyObject* boost::python::objects::make_instance_impl<T, Holder, Derived>::execute(Arg&)’:  
include/boost/python/object/make_instance.hpp:35:36: error: invalid use of incomplete type ‘PyTypeObject {aka struct _typeobject}’  
         PyObject* raw_result = type->tp_alloc(  
```  
  
I'm a little out of my depth here so some expertise would be welcome

---

## Comment 3

> Username: earonesty  
> Created at: 2020-05-20 17:06:29 UTC  
> Updated at: 2020-05-20 17:09:20 UTC  
> Url: https://github.com/boostorg/python/issues/221#issuecomment-631605111  

That error is because tp_alloc is not available in the limited api.   Follow https://www.python.org/dev/peps/pep-0384/ to fix.   Basically, you have to walk through all the failures, change boost to using the limited api for as many as you can (a good practice anyway), and disable the feature if you can't.  It's a big chore.

---

## Comment 4

> Username: AllSeeingEyeTolledEweSew  
> Created at: 2021-09-04 07:00:40 UTC  
> Url: https://github.com/boostorg/python/issues/221#issuecomment-912922901  

This is really important.  
  
Compilation times with boost are already huge, since boost is meant for large-scale C++ projects.  
  
Lack of support for `abi3` / `Py_LIMITED_API` means that users have to build and ship 5x as many artifacts, to cover all currently-supported python versions (3.6 through 3.10 as of writing).

---

## Comment 5

> Username: cas--  
> Created at: 2021-09-04 19:11:31 UTC  
> Updated at: 2021-09-04 19:25:58 UTC  
> Url: https://github.com/boostorg/python/issues/221#issuecomment-913024728  

I thought I would do some research on this to determine what effort is required to get to a stable abi. The only downsides I have read is that it might come at a small performance cost.  
  
cPython Reference:  
 * https://www.python.org/dev/peps/pep-0384/ (Although quite outdated now)  
 * https://docs.python.org/3.11/c-api/stable.html  
 * https://github.com/python/cpython/blob/main/Tools/scripts/abitype.py  
  
Push to maintain the stable abi:  
 * https://pythoncapi.readthedocs.io/stable_abi.html  
 * https://github.com/encukou/abi3 resulting in [pep-0652](https://www.python.org/dev/peps/pep-0652/)  
  
Other projects with in-progress work:  
* https://github.com/swig/swig/issues/1009 and wip: https://github.com/swig/swig/pull/1613  
* https://sourceforge.net/p/cxx/patches/28/  
  
Building the pip wheel with `py_limited_api`:  
* https://stackoverflow.com/a/66055366/175584  
* https://github.com/pypa/cibuildwheel/issues/78  
  
For the current code there is usage of fields that will need alternatives to just using the `Py_` prefix:  
* `tp_name` e.g. `Py_TYPE(callable)->tp_name` see: https://bugs.python.org/issue42035  
* `tp_dict` ?  
* `tp_flags` -\> PyType_GetFlags  
* `tp_basicsize` PyType_Spec?  
* ~~`tp_as_number`~~ _PyNumberMethods are only used in Python 2 blocks_

---

## Comment 6

> Username: cas--  
> Created at: 2021-09-04 19:13:47 UTC  
> Url: https://github.com/boostorg/python/issues/221#issuecomment-913025099  

@AllSeeingEyeTolledEweSew I am not sure whether discussing on the boost mailing lists might get more traction?
