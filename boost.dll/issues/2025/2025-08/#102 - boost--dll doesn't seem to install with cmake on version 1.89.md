# #102 - boost::dll doesn't seem to install with cmake on version 1.89 [Closed]

> Username: bgemmill  
> Created at: 2025-08-15 01:37:44 UTC  
> Updated at: 2025-10-24 15:59:21 UTC  
> Closed at: 2025-10-24 15:59:21 UTC  
> Url: https://github.com/boostorg/dll/issues/102  

This report is duplicated here as per @pdimov [in the original](https://github.com/boostorg/cmake/issues/84).  
  
  
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
> Created at: 2025-08-15 16:36:31 UTC  
> Url: https://github.com/boostorg/dll/issues/102#issuecomment-3192109318  

As I said in https://github.com/boostorg/cmake/issues/84, it looks like https://github.com/boostorg/dll/pull/100 broke things.  
  
If you're going to make changes to the CMakeLists.txt file, you need CI that tests installation (and ideally also CI that tests add_subdirectory use.)

---

## Comment 2

> Username: Osyotr  
> Created at: 2025-08-17 09:04:49 UTC  
> Url: https://github.com/boostorg/dll/issues/102#issuecomment-3194245051  

I still think that the original change is wrong. See discussion in https://github.com/boostorg/dll/pull/94.

---

## Comment 3

> Username: yurybura  
> Created at: 2025-08-17 21:59:49 UTC  
> Url: https://github.com/boostorg/dll/issues/102#issuecomment-3194678605  

@pdimov Please check out my patch #103. Thank you.

---

## Comment 4

> Username: apolukhin  
> Created at: 2025-10-24 15:59:21 UTC  
> Url: https://github.com/boostorg/dll/issues/102#issuecomment-3443882763  

Looks like the issue was fixed in https://github.com/boostorg/dll/pull/103
