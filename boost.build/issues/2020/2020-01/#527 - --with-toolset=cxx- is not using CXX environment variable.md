# #527 - "-with-toolset=cxx" is not using CXX environment variable [Closed]

> Username: firewave  
> Created at: 2020-01-15 17:27:22 UTC  
> Updated at: 2020-01-16 10:22:39 UTC  
> Closed at: 2020-01-16 10:22:38 UTC  
> Url: https://github.com/boostorg/build/issues/527  

When specifying `-with-toolset=cxx` it will try to invoke `cxx` as a command instead of using the compiler specified in the `CXX` environment variable.  
  
The problem is quite obvious in the code which always just assigns `cxx`.  
https://github.com/boostorg/build/blob/ab5ce1e4686715bc83eeb28b809b9268d6cb2f63/src/engine/build.sh#L354  
  
This also applies to `cross-cxx`. The old `-with-toolset=cc` was correctly using the `CC` environment variable.

---

## Comment 1

> Username: firewave  
> Created at: 2020-01-15 17:33:10 UTC  
> Url: https://github.com/boostorg/build/issues/527#issuecomment-574769037  

See https://github.com/conan-io/conan-center-index/issues/193 for more context.

---

## Comment 2

> Username: firewave  
> Created at: 2020-01-16 10:22:38 UTC  
> Url: https://github.com/boostorg/build/issues/527#issuecomment-575084795  

The code is actually working fine. The problem was the boost recipe removing `CXX` from the environment.  
  
Sorry about that, but I have been staring this issue (and several related ones) in the face for so long now I have been looking in all the wrong places and overlooked the obvious one.
