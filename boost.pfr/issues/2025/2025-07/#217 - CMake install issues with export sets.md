# #217 - CMake install issues with export sets [Closed]

> Username: Caointean  
> Created at: 2025-07-17 13:16:02 UTC  
> Updated at: 2025-07-17 21:03:35 UTC  
> Closed at: 2025-07-17 21:03:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/217  

Hello there !  
  
I ran into some issues when trying to install my library with a dependency on PFR (retrieved through FetchContent) with CMake exports sets and all.  
  
```text  
CMake Error in CMakeLists.txt:  
  install(EXPORT "myLibTargets" ...) includes target "myLib" which  
  requires target "boost_pfr" that is not in any export set.  
```  
  
and then when adding boost_pfr to myLib export set:  
  
```text  
CMake Error in build/_deps/pfr-src/CMakeLists.txt:  
  Target "boost_pfr" INTERFACE_INCLUDE_DIRECTORIES property contains path:  
  
    "/<an absolute path to>/build/_deps/pfr-src/include"  
  
  which is prefixed in the build directory.  
  
  
CMake Error in build/_deps/pfr-src/CMakeLists.txt:  
  Target "boost_pfr" INTERFACE_INCLUDE_DIRECTORIES property contains path:  
  
    "/<an absolute path to>/build/_deps/pfr-src/include"  
  
  which is prefixed in the build directory.Target "boost_pfr"  
  INTERFACE_INCLUDE_DIRECTORIES property contains path:  
  
    "/<an absolute path to>/build/_deps/pfr-src/include"  
  
  which is prefixed in the source directory.  
```  
  
I've seen that PFR's CMake is pretty basic and I could add some changes to it that would solve the issue but I am unsure of the ripple effects of these changes. So before a set out on writing a PR I just need to know what test/compilations to run, and is the target max cmake version set in stone ?

---

## Comment 1

> Username: Caointean  
> Created at: 2025-07-17 21:03:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/217#issuecomment-3085464221  

My bad Irealise I was using an outdated fork sorry :(
