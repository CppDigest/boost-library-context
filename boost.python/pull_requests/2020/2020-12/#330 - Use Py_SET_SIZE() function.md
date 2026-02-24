# #330 Use Py_SET_SIZE() function [Closed]

> Username: vstinner  
> Created at: 2020-12-09 01:06:53 UTC  
> Updated at: 2021-01-04 10:02:34 UTC  
> Closed at: 2021-01-04 06:33:30 UTC  
> Url: https://github.com/boostorg/python/pull/330  

Replace "Py_TYPE(obj)=type;" with "Py_SET_SIZE(obj, type);". The  
Py_SET_SIZE() was added to Python 3.9: copy pythoncapi_compat.h  
header to src/object/. The file comes from the project:  
https://github.com/pythoncapi/pythoncapi_compat  
  
On Python 3.10, Py_SET_SIZE() should now be used rather than using  
Py_TYPE() as an l-value:  
https://docs.python.org/dev/c-api/structures.html#c.Py_TYPE  
  
Context: https://bugs.python.org/issue39573

---

## Comment 1

> Username: vstinner  
> Created_at: 2020-12-09 01:07:06 UTC  
> Url: https://github.com/boostorg/python/pull/330#issuecomment-741367319  

See also https://github.com/boostorg/python/pull/329

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2020-12-09 01:09:17 UTC  
> Url: https://github.com/boostorg/python/pull/330#issuecomment-741370337  

This looks like a duplicate of https://github.com/boostorg/python/pull/328, doesn't it ?

---

## Comment 3

> Username: vstinner  
> Created_at: 2020-12-09 01:10:12 UTC  
> Url: https://github.com/boostorg/python/pull/330#issuecomment-741371704  

> This looks like a duplicate of #328, doesn't it ?  
  
PR #328 replaces `obj->ob_type` with `Py_TYPE()`. It doesn't need pythoncapi_compat.h, since Py_TYPE() exists since Python 2.6.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2020-12-09 01:10:54 UTC  
> Url: https://github.com/boostorg/python/pull/330#issuecomment-741372647  

Aren't you referring to https://github.com/boostorg/python/pull/329 instead ?

---

## Comment 5

> Username: vstinner  
> Created_at: 2020-12-09 01:13:04 UTC  
> Url: https://github.com/boostorg/python/pull/330#issuecomment-741375803  

> This looks like a duplicate of #328, doesn't it ?  
  
Oh wait, ignore my previous comment. I wrote 2 PRs and I didn't notice that you're a talking about a 3rd PR.  
  
Yes, #328 is the same fix, but it doesn't use pythoncapi_compat.h. I propose to use it to avoid having to reimplemented the compatibility layer for old Python versions:  
  
```  
#if PY_VERSION_HEX < 0x030900A4  
#  define Py_SET_TYPE(obj, type) ((Py_TYPE(obj) = (type)), (void)0)  
#  define Py_SET_SIZE(obj, size) ((Py_SIZE(obj) = (size)), (void)0)  
#endif  
```  
  
pythoncapi_compat.h uses static inline functions rather than macros.

---

## Comment 6

> Username: vstinner  
> Created_at: 2020-12-09 01:16:05 UTC  
> Url: https://github.com/boostorg/python/pull/330#issuecomment-741380052  

Fedora uses a downstream patch to fix issue: https://src.fedoraproject.org/rpms/boost/blob/master/f/boost-1.73-python3.10.patch Copy of the boost specfile:  
  
```  
# https://bugzilla.redhat.com/show_bug.cgi?id=1896382  
# https://github.com/boostorg/python/issues/325  
Patch93: boost-1.73-python3.10.patch  
```

---

## Comment 7

> Username: vstinner  
> Created_at: 2020-12-09 09:35:06 UTC  
> Url: https://github.com/boostorg/python/pull/330#issuecomment-741652271  

> APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2019  
  
This job failed because it failed to locate the C++ compiler, whereas the two other AppVeyor jobs passed:  
  
```  
set FARGS=--log=summary --log=output --log=actions --log=commands --with-boost-include=%BOOST_PREFIX% target.arch=%ARCH% cxx.name=msvc cxx.version=%MSVC%  
faber %FARGS% config || type config.log  
Error: C:\Python36-x64\lib\site-packages\faber\tools\cxx.py:64: no C++ compiler found matching <set cxx.name=msvc cxx.version=16.7.4 target.arch=x86_64>.  
faber %FARGS% -j8  
Error: C:\Python36-x64\lib\site-packages\faber\tools\cxx.py:64: no C++ compiler found matching <set cxx.name=msvc cxx.version=16.7.4 target.arch=x86_64>.  
Command exited with code 1  
```

---

## Comment 8

> Username: stefanseefeld  
> Created_at: 2021-01-04 06:33:30 UTC  
> Url: https://github.com/boostorg/python/pull/330#issuecomment-753786273  

Closed as obsolete.

---
