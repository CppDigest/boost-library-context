# #103 Adapt `test/CMakeLists.txt` for standalone vs. super-project builds [Merged]

> Username: chandryan  
> Created at: 2025-09-15 20:58:54 UTC  
> Updated at: 2025-09-28 04:02:31 UTC  
> Merged at: 2025-09-16 04:41:58 UTC  
> Closed at: 2025-09-16 04:41:58 UTC  
> Url: https://github.com/boostorg/msm/pull/103  

Adapt `test/CMakeLists.txt` as discussed in https://github.com/boostorg/msm/pull/99:  
  
- by linking against `Boost::msm` when built as part of the super-project  
- with `find_package` and selective linking when built standalone (might not be required when sticking to build as super-project)

---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2025-09-16 07:01:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/msm/pull/103#pullrequestreview-3227894922  

📁 test/CMakeLists.txt

```diff
  14 |+ get_filename_component(PARENT_DIR ${CMAKE_CURRENT_SOURCE_DIR} DIRECTORY)
  15 |+ if(PARENT_DIR STREQUAL CMAKE_SOURCE_DIR)
  16 |+     target_include_directories(boost_msm_tests PRIVATE ../include)
```

> Username: Flamefire  
> Created_at: 2025-09-16 07:01:06 UTC  
> Updated_at: 2025-09-16 07:01:26 UTC  
> Url: https://github.com/boostorg/msm/pull/103#discussion_r2351120865  

If you check if the build happens from the parent directory, why don't you link against the target created there too? I.e. `    target_link_libraries(boost_msm_tests Boost::msm)` can be unconditional and replace the `target_include_directories` line.  
  
But as you link a lot of other Boost libraries there this won't work as the targets for e.g. `    Boost::assert` will be missing.  
  
What is your intention? Running `cmake /path/to/msm` or `cmake /path/to/msm/test`?  
  
For the latter the check needs to be `if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)` or it will never match  
  
For the former you could move the logic to the main CML and just add this before `target_link_libraries`:  
```  
if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)  
    find_package(Boost COMPONENTS serialization any preprocessor type_traits [...] REQUIRED)  
endif()  
```  
  
The the tests just link against `Boost::msm` and the whole if-else here is not required.  
  
I have something similar in Nowide: https://github.com/boostorg/nowide/blob/c05f07fedf95cadbb8402f0af62c6333313758cf/CMakeLists.txt#L84-L97  
  
There I just search for Boost in general, assume a full build with all libraries available and link against the header-target `Boost::boost` In your case that could be:  
  
  
```cmake  
if(BOOST_SUPERPROJECT_SOURCE_DIR)  
  target_link_libraries(boost_msm  
  INTERFACE  
    Boost::any  
    Boost::assert  
    Boost::bind  
    Boost::circular_buffer  
    Boost::config  
    Boost::core  
    Boost::function  
    Boost::fusion  
    Boost::mp11  
    Boost::mpl  
    Boost::parameter  
    Boost::phoenix  
    Boost::preprocessor  
    Boost::proto  
    Boost::serialization  
    Boost::tuple  
    Boost::type_traits  
    Boost::typeof  
)  
else()  
  # Standalone build, requires (almost) all (header-only) Boost libraries to be installed, see above list  
  if(NOT TARGET Boost::boost)  
    find_package(Boost 1.56 REQUIRED)  
  endif()  
  target_link_libraries(boost_msm INTERFACE Boost::boost)  
endif()  
```

> Username: chandryan  
> Created_at: 2025-09-18 07:13:35 UTC  
> Url: https://github.com/boostorg/msm/pull/103#discussion_r2357785731  

Thank you for your suggestions for cleaning up the CMakeList.txt's, I appreciate it :)  
  
The intention was the former: Calling `cmake` from msm, though I just tried out building the tests as part of the super-project and it works well for me - I'll stick to that in the future.  
  
The use case of building the tests standalone was rather due to missing knowledge about Boost on my side than something useful. MSM is the first Boost project I contributed to and when I first set it up, I neither invoked `b2` properly, nor did I know about the approach using the super-project for building and testing (thanks again for pointing that out).  
Those contents in `test/CMakeLists.txt` were the initial result of running the tests standalone with a half-baked Boost installation on my machine.  
  
Despite my need of running the tests standalone having vanished, I guess the option to build MSM standalone might be useful. I'll create another PR for this :)

> Username: chandryan  
> Created_at: 2025-09-18 07:20:14 UTC  
> Url: https://github.com/boostorg/msm/pull/103#discussion_r2357806159  

The PR is here: https://github.com/boostorg/msm/pull/107  
  
Would you like to take another look @Flamefire ?


---
