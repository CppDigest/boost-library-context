# #400 - Python 3.11 gives SystemError: type Boost.Python.enum has the Py_TPFLAGS_HAVE_GC flag but has no traverse function [Open]

> Username: owillebo  
> Created at: 2022-10-30 10:18:53 UTC  
> Updated at: 2025-06-27 18:10:07 UTC  
> Url: https://github.com/boostorg/python/issues/400  

Up to Python 3.10 our Boost Python binding ran fine.  
As of Python 3.11 we get the following error upon import of the binding;  
  
`SystemError: type Boost.Python.enum has the Py_TPFLAGS_HAVE_GC flag but has no traverse function`  
  
Running;  
Python 3.11.0 (main, Oct 24 2022, 18:26:48) [MSC v.1933 64 bit (AMD64)] on win32  
boost_1_80_0  
b2 -j%NUMBER_OF_PROCESSORS% python=3.11 --with-python --stage-libdir=lib64-msvc-14.3 --build-type=complete toolset=msvc-14.3 address-model=64 architecture=x86 debug-symbols=on stage  
  
If I remove line 116 of libs\python\src\object\enum.cpp I don't get the error.  
  
```  
  
          #if PY_VERSION_HEX < 0x03000000  
              | Py_TPFLAGS_CHECKTYPES  
          #endif  
              | Py_TPFLAGS_HAVE_GC                    /* <============== remove this line */  
              | Py_TPFLAGS_BASETYPE,                  /* tp_flags */  
  
```  
  
Is it safe to do this?

---

## Comment 1

> Username: AllSeeingEyeTolledEweSew  
> Created at: 2022-11-09 00:35:43 UTC  
> Url: https://github.com/boostorg/python/issues/400#issuecomment-1308030455  

#385

---

## Comment 2

> Username: owillebo  
> Created at: 2022-12-20 14:44:09 UTC  
> Url: https://github.com/boostorg/python/issues/400#issuecomment-1359479913  

Fixed;  
  
https://github.com/boostorg/python/commit/a218babc8daee904a83f550fb66e5cb3f1cb3013

---

## Comment 3

> Username: Sinan81  
> Created at: 2025-06-27 18:10:07 UTC  
> Url: https://github.com/boostorg/python/issues/400#issuecomment-3013978367  

Is it advisable to patch boost <=1.80 if python >=3.11?
