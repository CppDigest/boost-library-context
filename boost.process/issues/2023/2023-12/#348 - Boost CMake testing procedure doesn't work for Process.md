# #348 - Boost CMake testing procedure doesn't work for Process [Open]

> Username: pdimov  
> Created at: 2023-12-17 00:49:24 UTC  
> Updated at: 2023-12-17 00:49:24 UTC  
> Url: https://github.com/boostorg/process/issues/348  

The [documented testing procedure](https://github.com/boostorg/cmake#testing-boost-with-cmake)  
  
```  
mkdir __build && cd __build  
cmake -DBUILD_TESTING=ON -DBOOST_INCLUDE_LIBRARIES=process ..  
cmake --build . --target tests  
ctest --output-on-failure --no-tests=error  
```  
  
fails for Boost.Process, because the test executables aren't built by the target `tests`. BoostTest handles this automatically, but for tests declared manually, one needs to first declare the `tests` target if not present  
  
```  
if(NOT TARGET tests)  
    add_custom_target(tests)  
endif()  
```  
  
and then for each test executable, use `add_dependencies(test my_test_executable)`.  
  
(For this to keep working after the eventual fix, it might be a good idea to add it to CI.)  
  
It's also good practice to make the test executables EXCLUDE_FOR_ALL, so that building the `tests` target builds them, but building the default target doesn't, but this step is not in principle required.
