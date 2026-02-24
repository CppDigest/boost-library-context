# #664 - [Suggestion] Use CMake 3.19 "Interface Library" for easier "header only" support [Closed]

> Username: werto87  
> Created at: 2022-01-01 16:09:02 UTC  
> Updated at: 2022-01-02 19:37:27 UTC  
> Closed at: 2022-01-02 19:37:27 UTC  
> Url: https://github.com/boostorg/json/issues/664  

Currently to use boost/json as header only library the user has to "#include <boost/json/src.hpp>" in exactly one translation unit. Also, this library uses "*.ipp" files instead of "normal" source files. I think it's possible to use CMake 3.19 [Interface Library](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#interface-libraries) to create a target which has all the headers and sources but does not get build as library and allows the user of this library to import all the headers and sources from boost/json.  
  
I suggest to use CMake 3.19 [Interface Library](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#interface-libraries) which brings two benefits:  
  
1. The user can link against boost header only and does not need to provide the one translation unit "#include <boost/json/src.hpp>"  
2. "*.ipp" files can be replaced with "normal" source files. (I am not an "ipp" file expert, but I think boost/json uses them instead of normal source files because CMake would try to build them?)

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-01-01 16:38:17 UTC  
> Url: https://github.com/boostorg/json/issues/664#issuecomment-1003582227  

Interface libraries has been in CMake since version 2-something, iirc, so either you meant some particular 3.19 feature, or the title of the issue is misleading.  
  
The use of ipp extension is deliberate, corresponds to their purpose and has absolutely nothing to do with CMake.  
  
Anyways, can you present a specific use case for using header-only version with CMake?

---

## Comment 2

> Username: werto87  
> Created at: 2022-01-02 19:37:27 UTC  
> Url: https://github.com/boostorg/json/issues/664#issuecomment-1003764448  

Thanks for clearing things up. I am sorry I confused some things.
