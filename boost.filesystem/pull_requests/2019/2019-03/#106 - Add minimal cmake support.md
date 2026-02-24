# #106 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-08 15:10:57 UTC  
> Updated at: 2019-04-15 09:24:46 UTC  
> Merged at: 2019-04-15 09:10:25 UTC  
> Closed at: 2019-04-15 09:10:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/106  

Functionality:  
  
- CMake file only supports add_subdirectory workflow.  
- Compiles boost filesystem (without name mangling).  
- Provides target Boost::filesystem to link against.  
- Does NOT compile/run unit tests (yet) and  
  doesn't support installation.  
  
For basic testing:  
  
1) Make sure you have checked out the latest (develop) version of all other boost libraries you are depending on (directly or indirectly) into sibling folders of this library (e.g. recursively check out boostorg/boost).   
  
  
2) Copy a file like this: https://github.com/Mike-Devel/boost/blob/boost_cmake_demo/CMakeLists.txt into the boost root directory. Essentially, that file will simply call `add_subdirectory( )` on all boost libraries with a cmake file to make sure all dependencies are known to cmake. However, it will also ignore some libraries that are known to be troublesome with the add_subdirectory workflow.  
  
  
3) Then (from boost root)   
  
         mkdir __build__ && cd __build__  
         cmake ..  
         cmake --build libs/filesystem

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-04-14 17:44:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/106#issuecomment-483026887  

Dependency on Boost.WinAPI is missing.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-04-15 08:50:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/106#issuecomment-483164979  

Thanks for spotting this. Must have forgotten the `--track-sources` option when checking with boostdep

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-04-15 09:10:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/106#issuecomment-483171844  

Thanks.

---
