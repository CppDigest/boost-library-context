# #385 Fix enum_type_object type on Python 3.11 [Merged]

> Username: vstinner  
> Created at: 2022-04-25 08:53:53 UTC  
> Updated at: 2022-04-26 13:50:37 UTC  
> Merged at: 2022-04-26 13:42:27 UTC  
> Closed at: 2022-04-26 13:42:27 UTC  
> Url: https://github.com/boostorg/python/pull/385  

The enum_type_object type inherits from PyLong_Type which is not tracked  
by the GC. Instances doesn't have to be tracked by the GC: remove the  
Py_TPFLAGS_HAVE_GC flag.  
  
The Python C API documentation says:  
  
    "To create a container type, the tp_flags field of the type object  
    must include the Py_TPFLAGS_HAVE_GC and provide an implementation of  
    the tp_traverse handler."  
  
https://docs.python.org/dev/c-api/gcsupport.html  
  
The new exception was introduced in Python 3.11 by:  
https://github.com/python/cpython/issues/88429

---

## Comment 1

> Username: vstinner  
> Created_at: 2022-04-25 08:57:41 UTC  
> Url: https://github.com/boostorg/python/pull/385#issuecomment-1108280287  

A Boost Python enum also has a name, but a name type is a Python str string which is not tracked by the GC. Example (in Python 3.11):  
  
```py  
>>> import gc  
>>> my_str="hello"  
>>> gc.is_tracked(my_str)  
False  
```  
  
So it's fine to no track Boost Python enums by the Python GC. Only objects which contain other objects must be tracked by the GC, like containers (list, dict, ...).  
  
Reference documentation about the Python GC: https://devguide.python.org/garbage_collector/

---

## Comment 2

> Username: vstinner  
> Created_at: 2022-04-25 08:58:54 UTC  
> Url: https://github.com/boostorg/python/pull/385#issuecomment-1108281820  

The "deploy documentation / deploy (pull_request)" CI job failed with:  
  
```  
Push the commit or tag  
  /usr/bin/git push origin gh-pages  
  remote: Permission to boostorg/python.git denied to github-actions[bot].  
  fatal: unable to access 'https://github.com/boostorg/python.git/': The requested URL returned error: 403  
  Error: Action failed with "The process '/usr/bin/git' failed with exit code 128"  
```  
  
It doesn't seem to be related to my change.

---

## Comment 3

> Username: vstinner  
> Created_at: 2022-04-26 13:20:17 UTC  
> Url: https://github.com/boostorg/python/pull/385#issuecomment-1109789347  

> Test Windows / build (3.7) (pull_request) Failing after 32s  
  
It seems to be unrelated to my change:  
  
> Could not find MSBuild version with C++ support. VS2015, VS2017, or VS2019 (with C++) needs to be installed.

---

## Comment 4

> Username: vstinner  
> Created_at: 2022-04-26 13:26:26 UTC  
> Url: https://github.com/boostorg/python/pull/385#issuecomment-1109796347  

imath test suite pass with this change, it has been tested in a Fedora COPR for Python 3.11 with this change, and imath package built successfully: https://copr.fedorainfracloud.org/coprs/g/python/python3.11/builds/  
  
> 4334582 | imath | 3.1.5-1.fc37 | an hour ago | 15 minutes | succeeded  
  
Without this change, imath raises a SystemError "type Boost.Python.enum has the Py_TPFLAGS_HAVE_GC flag but has no traverse function" on Python 3.11: https://github.com/AcademySoftwareFoundation/Imath/issues/253  
  
Fedora issues about Boost Python on Python 3.11:  
  
* imath: https://bugzilla.redhat.com/show_bug.cgi?id=2069676  
* deluge: https://bugzilla.redhat.com/show_bug.cgi?id=2070428

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2022-04-26 13:42:20 UTC  
> Url: https://github.com/boostorg/python/pull/385#issuecomment-1109814350  

Thanks @vstinner for this PR. I confirm that the CI build failure is unrelated (and is in fact a misconfiguration in the CI workflow logic, as PRs from other repos lack permission to push doc updates).

---
