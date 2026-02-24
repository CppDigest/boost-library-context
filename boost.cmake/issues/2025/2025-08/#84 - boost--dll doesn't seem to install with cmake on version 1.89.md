# #84 - boost::dll doesn't seem to install with cmake on version 1.89 [Closed]

> Username: bgemmill  
> Created at: 2025-08-14 19:59:54 UTC  
> Updated at: 2025-12-15 02:56:02 UTC  
> Closed at: 2025-12-15 02:56:02 UTC  
> Url: https://github.com/boostorg/cmake/issues/84  

I tried moving to boost 1.89 from 1.88 this afternoon, and found with the cmake installer that boost::dll doesn't seem to get installed with the newest version.  
  
With boost 1.88, the cmake build output would contain lines like:  
```  
[build] -- Installing: /<relative-build-path>/boost/include/boost/dll/detail/posix/shared_library_impl.hpp  
```  
But 1.89 doesn't seem to have any.  
  
This can be reproduced with:  
```  
git clone --recurse-submodules https://github.com/boostorg/boost --depth 1  
cd boost  
mkdir build  
mkdir install  
cd build  
cmake -DCMAKE_INSTALL_PREFIX=../install ..  
cmake --build . --target install -j  
cd ../install/include/boost  
cd dll # No such file or directory  
```  
  
Did something happen to how the dll project uses cmake between version 1.88 and 1.89?

---

## Comment 1

> Username: pdimov  
> Created at: 2025-08-14 21:41:30 UTC  
> Url: https://github.com/boostorg/cmake/issues/84#issuecomment-3189945485  

I'll take a look later, but in the meantime, you should file an issue against boostorg/dll directly.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-08-14 21:42:44 UTC  
> Url: https://github.com/boostorg/cmake/issues/84#issuecomment-3189947766  

It's probably this change https://github.com/boostorg/dll/pull/100 that broke it.

---

## Comment 3

> Username: nigels-com  
> Created at: 2025-12-14 08:02:17 UTC  
> Url: https://github.com/boostorg/cmake/issues/84#issuecomment-3650450749  

@bgemmill Appears to be resolved in the current [1.90.0](https://www.boost.org/releases/1.90.0/) release.  
If you could verify and close this ticket, would appreciate it.

---

## Comment 4

> Username: bgemmill  
> Created at: 2025-12-15 02:56:02 UTC  
> Url: https://github.com/boostorg/cmake/issues/84#issuecomment-3652681033  

1.90 works for me!
