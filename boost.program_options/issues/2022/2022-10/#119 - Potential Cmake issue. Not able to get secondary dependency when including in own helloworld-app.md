# #119 - Potential Cmake issue. Not able to get secondary dependency when including in own helloworld-app [Closed]

> Username: bratne  
> Created at: 2022-10-30 12:16:29 UTC  
> Updated at: 2026-02-19 19:58:36 UTC  
> Closed at: 2026-02-19 19:58:36 UTC  
> Url: https://github.com/boostorg/program_options/issues/119  

I trying to include program_options in a simple testapp, by using CMake.  
  
```cmake  
include(FetchContent)  
FetchContent_Declare (  
    boost_program_options  
    GIT_REPOSITORY https://github.com/boostorg/program_options.git     
    GIT_TAG boost-1.80.0  
)  
FetchContent_MakeAvailable(boost_program_options)  
add_executable(helloworld main.cpp)  
target_link_libraries(helloworld PRIVATE boost_program_options)   
```  
  
Trying to build this yields  
```  
[cmake] CMake Error at CMakeLists.txt:15 (add_executable):  
[cmake]   Target "helloworld" links to target "Boost::any" but the target was not  
[cmake]   found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
[cmake]   an ALIAS target is missing?  
```

---

## Comment 1

> Username: ngunderson  
> Created at: 2023-07-21 18:27:19 UTC  
> Updated at: 2023-07-21 18:28:04 UTC  
> Url: https://github.com/boostorg/program_options/issues/119#issuecomment-1646088628  

`Boost::any` is a separate library/target than program_options. You could add fetching `https://github.com/boostorg/any.git` to retrieve this target, though you may end up needing another library that Boost Program Options also depends on. You could instead try fetching the super project boost repository than has all Boost libraries, `https://github.com/boostorg/boost`. This will give you all Boost libraries, but you should still only have access to and build the explicit targets you link to, like (`Boost::program_options`).
