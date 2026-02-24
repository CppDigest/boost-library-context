# #325 - PEP-0620 Disallows using Py_TYPE() as l-value [Closed]

> Username: jcpunk  
> Created at: 2020-10-16 19:48:31 UTC  
> Updated at: 2021-04-18 16:32:44 UTC  
> Closed at: 2021-04-18 16:32:44 UTC  
> Url: https://github.com/boostorg/python/issues/325  

https://www.python.org/dev/peps/pep-0620/  
  
This will probably impact `include/boost/python/lvalue_from_pytype.hpp` along with other elements of this library.

---

## Comment 1

> Username: jwakely  
> Created at: 2020-11-10 16:42:35 UTC  
> Url: https://github.com/boostorg/python/issues/325#issuecomment-724823104  

Boost 1.73.0 cannot build with Python 3.10.0a2, see https://bugzilla.redhat.com/show_bug.cgi?id=1896382  
  
```  
In file included from /usr/include/python3.10/pytime.h:6,  
                 from /usr/include/python3.10/Python.h:85,  
                 from ./boost/python/detail/wrap_python.hpp:178,  
                 from ./boost/python/detail/prefix.hpp:13,  
                 from ./boost/python/object_core.hpp:10,  
                 from ./boost/python/object/enum_base.hpp:8,  
                 from libs/python/src/object/enum.cpp:6:  
libs/python/src/object/enum.cpp: In function 'boost::python::api::object boost::python::objects::{anonymous}::new_enum_type(const char*, const char*)':  
/usr/include/python3.10/object.h:140:29: error: lvalue required as left operand of assignment  
  140 | #define Py_TYPE(ob) _Py_TYPE(_PyObject_CAST_CONST(ob))  
      |                     ~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs/python/src/object/enum.cpp:156:11: note: in expansion of macro 'Py_TYPE'  
  156 |           Py_TYPE(&enum_type_object) = incref(&PyType_Type);  
      |           ^~~~~~~  
```

---

## Comment 2

> Username: jwakely  
> Created at: 2020-11-14 01:13:07 UTC  
> Updated at: 2020-11-14 01:13:29 UTC  
> Url: https://github.com/boostorg/python/issues/325#issuecomment-727107195  

[boost-1.73-py310.txt](https://github.com/boostorg/python/files/5539965/boost-1.73-py310.txt)  
  
Here's a patch against Boost 1.73.0 that allows it to build with Python 3.10

---

## Comment 3

> Username: jcpunk  
> Created at: 2020-11-14 17:28:41 UTC  
> Url: https://github.com/boostorg/python/issues/325#issuecomment-727239158  

Can you post a PR to make merging easier?

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2020-11-14 17:41:36 UTC  
> Updated at: 2020-11-14 17:41:55 UTC  
> Url: https://github.com/boostorg/python/issues/325#issuecomment-727240884  

I already started to morph  @jwakely 's patch into a branch yesterday, and just turned it into a PR: https://github.com/boostorg/python/pull/328

---

## Comment 5

> Username: jwakely  
> Created at: 2020-11-14 19:08:11 UTC  
> Url: https://github.com/boostorg/python/issues/325#issuecomment-727252058  

Thanks, Stefan. I didn't send a PR because my priority was just getting the fedora package building, and that's all I had time for. I'm glad that was still useful.

---

## Comment 6

> Username: jwakely  
> Created at: 2020-11-18 19:29:49 UTC  
> Url: https://github.com/boostorg/python/issues/325#issuecomment-729904254  

Th Python 3.10 change has been reverted, so Boost.Python doesn't need to change now (although the changes shouldn't hurt either).

---

## Comment 7

> Username: jcpunk  
> Created at: 2020-11-18 20:19:02 UTC  
> Url: https://github.com/boostorg/python/issues/325#issuecomment-729929614  

All told, I'd rather have this change in place should the plan reappear in a later python.

---

## Comment 8

> Username: vstinner  
> Created at: 2020-12-09 01:17:35 UTC  
> Url: https://github.com/boostorg/python/issues/325#issuecomment-741382066  

> I already started to morph @jwakely 's patch into a branch yesterday, and just turned it into a PR: #328  
  
I proposed an alternative fix: PR #330 which uses pythoncapi_compat.h to avoid having to reimplement new (Python 3.9) Py_SET_SIZE() function for old Python versions.  
  
https://github.com/pythoncapi/pythoncapi_compat takes care of it for you.
