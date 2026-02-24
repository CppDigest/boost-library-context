# #170  Add an explicit destructor function argument to registry::push_back()  [Open]

> Username: res2k  
> Created at: 2017-11-01 10:32:04 UTC  
> Updated at: 2020-11-14 02:30:48 UTC  
> Url: https://github.com/boostorg/python/pull/170  

This patch adds an explicit 'destruct' function argument to `registry::push_back()`.  
  
Motivation:  
See the 'opaque_ref' test in commit 834f0d1. This creates a wrapper for a C++ type where only the declaration is known. This may happen if you want to wrap functions/methods which take a const ref to a certain type as an argument, but instances of that type are produced elsewhere (perhaps a different Python module).  
As it is now the 'opaque_ref' test fails because the associated Python module references `~Opaque()` - which is undefined. This is also counterintuitive, since Opaque is actually not constructed anywhere!  
The culprit is `rvalue_from_python_data<T>::~rvalue_from_python_data()` which unconditionally references the dtor of T. If we replace this dtor call with an explicit destructor function we only need a dtor reference if we're actually creating an instance.  
  
Source compatibility?:  
Adding a parameter to `registry::push_back()` may break existing sources. It's not clear to me whether it's supposed to be a "stable" API or whether breakage is acceptable. If it's the former I'd look into creating a source-compatible variant.

---
