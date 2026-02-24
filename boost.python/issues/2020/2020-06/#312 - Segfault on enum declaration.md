# #312 - Segfault on enum declaration [Open]

> Username: nyibbang  
> Created at: 2020-06-30 09:57:24 UTC  
> Updated at: 2022-05-23 14:23:45 UTC  
> Url: https://github.com/boostorg/python/issues/312  

Hello,  
  
I'm having an issue on our  [libqi-python project](https://github.com/aldebaran/libqi-python) using boost-python 1.64, with Python 3.8 on a 32bits (x86) system. I could not find an existing issue related to this problem so I'm creating a new one.  
  
In one of our files, we [export an enum to python](https://github.com/aldebaran/libqi-python/blob/0a0105e888f4d50f6c79e7aeda24a36e264fbaef/src/pyfuture.cpp#L276):  
  
```cpp  
      boost::python::enum_<qi::FutureTimeout>("FutureTimeout")  
          .value("None", qi::FutureTimeout_None)  
          .value("Infinite", qi::FutureTimeout_Infinite);  
```  
  
with the enum being declared in our [main project (libqi)](https://github.com/aldebaran/libqi) at [this line](https://github.com/aldebaran/libqi/blob/7929251d5f17f6ee91a619692398e207bf52b9a4/qi/detail/future_fwd.hpp#L111):  
  
```cpp  
  enum FutureTimeout {  
    FutureTimeout_Infinite = INT_MAX, // TODO: replace by numeric_limits<int>::max() when we get  
                                      // rid of VS2013  
    FutureTimeout_None     = 0,  
  };  
```  
  
The crash occurs when we export the `Infinite` value of the enum which is set to `INT_MAX`. Weirdly enough it works fine on my host (Ubuntu 16.04 x86_64) but segfaults on one of our other 32 bits x86 system when cross-compiled.  
  
Here is the backtrace when the crash occurs:  
  
```cpp  
#0  0xf717282b in _Py_DECREF (filename=0xf74a8cdc "/usr/src/debug/boost/1.64.0-r0.1/recipe-sysroot/usr/include/python3.8/object.h", lineno=541, op=0x1) at /usr/include/python3.8/object.h:470  
#1  0xf7172886 in _Py_XDECREF (op=0x1) at /usr/include/python3.8/object.h:541  
#2  0xf71730f3 in boost::python::objects::enum_base::add_value (this=0xffffa8c8, name_=0xf749dcea "Infinite", value=2147483647) at libs/python/src/object/enum.cpp:219  
#3  0xf710d319 in boost::python::enum_<qi::FutureTimeout>::value (x=qi::FutureTimeout_Infinite, name=0xf749dcea "Infinite", this=0xffffa8c8)  
    at /home/fakeuser/toolchains/linux64-atom-yocto_dunfell-2.9/yocto-sdk/sysroots/core2-32-sbr-linux/usr/include/boost/python/enum.hpp:93  
#4  qi::py::export_pyfuture () at ../../../src/pyfuture.cpp:263  
#5  0xf7102114 in qi::py::export_all () at ../../../src/pyexport.cpp:24  
#6  0xf7164cd3 in init_module_qi_python () at ../../../src/boostpymain.cpp:21  
#7  0xf7196e1a in boost::detail::function::void_function_ref_invoker0<void (*)(), void>::invoke (function_obj_ptr=...) at ./boost/function/function_template.hpp:194  
#8  0xf6f3b53a in boost::function0<void>::operator() (this=0xffffaa6c)  
    at /home/fakeuser/toolchains/linux64-atom-yocto_dunfell-2.9/yocto-sdk/sysroots/core2-32-sbr-linux/usr/include/boost/function/function_template.hpp:673  
#9  0xf7195d59 in boost::python::handle_exception_impl (f=...) at libs/python/src/errors.cpp:25  
#10 0xf7196bb9 in boost::python::handle_exception<void (*)()> (f=0xf7164b80 <init_module_qi_python()>) at ./boost/python/errors.hpp:29  
#11 0xf719691f in boost::python::detail::(anonymous namespace)::init_module_in_scope (m=0xf7847140, init_function=0xf7164b80 <init_module_qi_python()>) at libs/python/src/module.cpp:24  
#12 0xf7196a5e in boost::python::detail::init_module (moduledef=..., init_function=0xf7164b80 <init_module_qi_python()>) at libs/python/src/module.cpp:43  
#13 0xf7164e41 in PyInit_qi_python () at ../../../src/boostpymain.cpp:11  
#14 0xf7dc8691 in _PyImport_LoadDynamicModuleWithSpec () from /usr/lib/libpython3.8.so.1.0  
#15 0xf7dc6055 in ?? () from /usr/lib/libpython3.8.so.1.0  
#16 0xf7d23e7f in ?? () from /usr/lib/libpython3.8.so.1.0  
#17 0xf7ce8cb6 in PyVectorcall_Call () from /usr/lib/libpython3.8.so.1.0  
#18 0xf7ce9000 in PyCFunction_Call () from /usr/lib/libpython3.8.so.1.0  
#19 0xf7cc60e1 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#20 0xf7da800b in _PyEval_EvalCodeWithName () from /usr/lib/libpython3.8.so.1.0  
#21 0xf7ce74f2 in _PyFunction_Vectorcall () from /usr/lib/libpython3.8.so.1.0  
#22 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#23 0xf7cc53b6 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#24 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#25 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#26 0xf7cc1dd5 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#27 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#28 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#29 0xf7cc3722 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#30 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#31 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#32 0xf7cc3722 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#33 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#34 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#35 0xf7cc3722 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#36 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#37 0xf7ce7b6a in ?? () from /usr/lib/libpython3.8.so.1.0  
#38 0xf7ce7db4 in _PyObject_CallMethodIdObjArgs () from /usr/lib/libpython3.8.so.1.0  
#39 0xf7dc7213 in PyImport_ImportModuleLevelObject () from /usr/lib/libpython3.8.so.1.0  
#40 0xf7cc5ac2 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#41 0xf7da800b in _PyEval_EvalCodeWithName () from /usr/lib/libpython3.8.so.1.0  
#42 0xf7da8355 in PyEval_EvalCodeEx () from /usr/lib/libpython3.8.so.1.0  
#43 0xf7da8390 in PyEval_EvalCode () from /usr/lib/libpython3.8.so.1.0  
#44 0xf7da4520 in ?? () from /usr/lib/libpython3.8.so.1.0  
#45 0xf7d23e7f in ?? () from /usr/lib/libpython3.8.so.1.0  
#46 0xf7ce8cb6 in PyVectorcall_Call () from /usr/lib/libpython3.8.so.1.0  
#47 0xf7ce9000 in PyCFunction_Call () from /usr/lib/libpython3.8.so.1.0  
#48 0xf7cc60e1 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#49 0xf7da800b in _PyEval_EvalCodeWithName () from /usr/lib/libpython3.8.so.1.0  
#50 0xf7ce74f2 in _PyFunction_Vectorcall () from /usr/lib/libpython3.8.so.1.0  
#51 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#52 0xf7cc53b6 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#53 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#54 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#55 0xf7cc1dd5 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#56 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#57 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#58 0xf7cc3722 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#59 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#60 0xf7cbf9da in ?? () from /usr/lib/libpython3.8.so.1.0  
#61 0xf7cc3722 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#62 0xf7cbe8c5 in ?? () from /usr/lib/libpython3.8.so.1.0  
#63 0xf7ce7b6a in ?? () from /usr/lib/libpython3.8.so.1.0  
#64 0xf7ce7db4 in _PyObject_CallMethodIdObjArgs () from /usr/lib/libpython3.8.so.1.0  
#65 0xf7dc7213 in PyImport_ImportModuleLevelObject () from /usr/lib/libpython3.8.so.1.0  
#66 0xf7cc5ac2 in _PyEval_EvalFrameDefault () from /usr/lib/libpython3.8.so.1.0  
#67 0xf7da800b in _PyEval_EvalCodeWithName () from /usr/lib/libpython3.8.so.1.0  
#68 0xf7da8355 in PyEval_EvalCodeEx () from /usr/lib/libpython3.8.so.1.0  
#69 0xf7da8390 in PyEval_EvalCode () from /usr/lib/libpython3.8.so.1.0  
#70 0xf7dde676 in ?? () from /usr/lib/libpython3.8.so.1.0  
#71 0xf7dde748 in ?? () from /usr/lib/libpython3.8.so.1.0  
#72 0xf7de07a4 in PyRun_StringFlags () from /usr/lib/libpython3.8.so.1.0  
#73 0xf7de0820 in PyRun_SimpleStringFlags () from /usr/lib/libpython3.8.so.1.0  
#74 0xf7dfcb3f in Py_RunMain () from /usr/lib/libpython3.8.so.1.0  
#75 0xf7dfd4cc in ?? () from /usr/lib/libpython3.8.so.1.0  
#76 0xf7dfd5a8 in Py_BytesMain () from /usr/lib/libpython3.8.so.1.0  
#77 0x56556087 in main ()  
```  
  
It seems the crash occurs from the `enum_base::add_value` function, at the `Py_XDECREF(p->name)` call. The object pointed by `enum_object* p` seems incorrect, with the `name` member having an invalid pointer value of `0x1`.  
  
From my investigations the size of int is 4 bytes on each system (therefore `INT_MAX` value is `2147483647` on both systems). I'm also pretty sure that it worked fine when when we had Python 3.5 on the 32 bits system.  
  
Another thing is I tried changing the value from `INT_MAX` to `INT_MAX / 2` and it seemed to fix the issue, but with `INT_MAX / 2 + 1` and above, it started segfaulting again.  
  
Thank you for your help.

---

## Comment 1

> Username: bernhardu  
> Created at: 2022-05-23 14:23:45 UTC  
> Url: https://github.com/boostorg/python/issues/312#issuecomment-1134745184  

Hello, this issue sounds similar to what I guess I have found in [Debian Bug#1009739](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1009739#25).  
  
I think the issue here is, that the member `PyLongObject` of the `struct enum_object` is intended to be a variable length object, and at i386 the function `PyLong_FromLong` assumes to be able to store more than one element into the `ob_digit` member of the `PyLongObject`. It does so by attempting to store a pointer value of `0x9e56f410` == `-1638468592` or unsigned `2656498704` in three elements in `ob_digit`.  
  
Storing two elements would be no issue due to the padding added in my case, but the third element of `ob_digit` occupies the same memory as the `enum_object.name` member. In my case it wrote also the value 1 there, which therefore is tried to be freed later in `enum_dealloc`.  
  
Adding a few bytes between the `PyLongObject base_object` and `PyObject *name` did work around the crash in my tests.  
  
https://github.com/boostorg/python/blob/a218babc8daee904a83f550fb66e5cb3f1cb3013/src/object/enum.cpp#L19-L27  
```c++  
(rr) ptype /o self  
type = struct boost::python::objects::enum_object {  
/*      0      |      16 */    PyLongObject base_object;  
/*     16      |       4 */    PyObject *name;  
                               /* total size (bytes):   20 */  
                             } *  
  
(rr) ptype /o PyLongObject  
type = struct _longobject {  
/*      0      |      12 */    PyVarObject ob_base;  
/*     12      |       2 */    digit ob_digit[1];  
/* XXX  2-byte padding   */  
                               /* total size (bytes):   16 */  
                             }  
```
