# #53 - Boost CMake testing procedure doesn't work for StaticString [Closed]

> Username: pdimov  
> Created at: 2023-12-17 00:54:56 UTC  
> Updated at: 2024-02-20 16:53:28 UTC  
> Closed at: 2024-02-20 16:53:28 UTC  
> Url: https://github.com/boostorg/static_string/issues/53  

The [documented testing procedure](https://github.com/boostorg/cmake#testing-boost-with-cmake)  
  
```  
mkdir __build && cd __build  
cmake -DBUILD_TESTING=ON -DBOOST_INCLUDE_LIBRARIES=static_string ..  
cmake --build . --target tests  
ctest --output-on-failure --no-tests=error  
```  
  
fails for Boost.StaticString, because the test executables aren't built by the target `tests`. BoostTest handles this automatically, but for tests declared manually, one needs to first declare the `tests` target if not present  
  
```  
if(NOT TARGET tests)  
    add_custom_target(tests)  
endif()  
```  
  
and then for each test executable, use `add_dependencies(test my_test_executable)`.  
  
Or, since there is already a target `boost_static_string_all_tests`, it should be enough to make it a dependency of `tests`: `add_dependencies(tests boost_static_string_all_tests)`.  
  
(For this to keep working after the eventual fix, it might be a good idea to add it to CI.)  
  
It's also good practice to make the test executables EXCLUDE_FOR_ALL, so that building the `tests` target builds them, but building the default target doesn't, but this step is not in principle required.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-16 10:46:23 UTC  
> Url: https://github.com/boostorg/static_string/issues/53#issuecomment-1948151656  

Is this going to be fixed? It breaks the tests of Mysql.

---

## Comment 2

> Username: sdkrystian  
> Created at: 2024-02-16 14:55:02 UTC  
> Url: https://github.com/boostorg/static_string/issues/53#issuecomment-1948531599  

@pdimov Looking into it

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-02-16 17:48:09 UTC  
> Url: https://github.com/boostorg/static_string/issues/53#issuecomment-1948977660  

Probably this one fixes it: https://github.com/boostorg/static_string/pull/54
