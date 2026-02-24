# #643 - Undefined behaviour when executable_path returns NULL [Closed]

> Username: corngood  
> Created at: 2020-08-15 23:57:10 UTC  
> Updated at: 2020-09-12 20:57:10 UTC  
> Closed at: 2020-09-12 20:57:10 UTC  
> Url: https://github.com/boostorg/build/issues/643  

Since 59b7a6dc69c3695581b38f4f1c643985895eeeaa, https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/engine/startup.cpp#L158 can result in undefined behaviour when `executable_path` returns null.  
  
I hit this when running from a shell on cygwin where argv[0] is "./b2".  The cygwin C++ throws an exception.  
  
Cygwin could probably use the linux implementation of `executable_path`, but as long as any of them can return NULL, this should probably be fixed.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-09-12 20:57:10 UTC  
> Url: https://github.com/boostorg/build/issues/643#issuecomment-691546921  

Resolved with https://github.com/boostorg/build/commit/1da0fe27d108c3d49652524d9c83aefe96d3a92a
