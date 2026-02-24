# #122 - find_package Boost system failure on cmake 3.27.6 [Closed]

> Username: daesdemon  
> Created at: 2023-09-29 08:47:37 UTC  
> Updated at: 2023-10-16 15:04:16 UTC  
> Closed at: 2023-10-16 15:04:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/122  

Hello,  
I don't know if this a particularity of my (not simple) configuration.  
I am running cmake 3.27.6 in collaboration with conan 2.0 for find_package   
  
The line 69(master) of cmakelists.txt isn't successful:  
  
`find_package(Boost 1.82 REQUIRED system OPTIONAL_COMPONENTS json context url)`  
  
```CMake Error at /usr/local/share/cmake-3.27/Modules/FindPackageHandleStandardArgs.cmake:230 (message):  
Could NOT find Boost: missing: system (found /workspaces/pulsar/build/conan/build/Debug/generators/BoostConfig.cmake (found suitable version "1.83.0", minimum required is "1.82"))  
  
Call Stack (most recent call first):  
  /usr/local/share/cmake-3.27/Modules/FindPackageHandleStandardArgs.cmake:243 (_FPHSA_FAILURE_MESSAGE)  
  /usr/local/share/cmake-3.27/Modules/FindPackageHandleStandardArgs.cmake:585 (_FPHSA_HANDLE_FAILURE_CONFIG_MODE)  
  /usr/local/share/cmake-3.27/Modules/FindBoost.cmake:626 (find_package_handle_standard_args)  
  conan_provider.cmake:514 (find_package)  
  ext/async/CMakeLists.txt:69 (find_package)  
```  
  
i had to replace it by:  
`find_package(Boost 1.82 REQUIRED COMPONENTS system OPTIONAL_COMPONENTS json context url)`

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-29 09:58:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/122#issuecomment-1740630369  

Interesting, probably because your boost comes from conan. I'll check next week.

---

## Comment 2

> Username: daesdemon  
> Created at: 2023-09-29 13:04:56 UTC  
> Url: https://github.com/boostorg/cobalt/issues/122#issuecomment-1740867549  

Yes probably the find_package implementation i use in conan2.0 is different from Cmake official.  
But for information, CMake FindBoost documentation shows a use of COMPONENTS too.  
https://cmake.org/cmake/help/latest/module/FindBoost.html  
And after some search, i didn't find any usage of find_package Boost  without COMPONENTS  
Are you sure it is not a typo ? :)
