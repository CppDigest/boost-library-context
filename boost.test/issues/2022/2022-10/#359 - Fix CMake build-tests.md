# #359 - Fix CMake build/tests [Open]

> Username: Flamefire  
> Created at: 2022-10-07 13:08:36 UTC  
> Updated at: 2022-10-07 13:08:36 UTC  
> Url: https://github.com/boostorg/test/issues/359  

I tried creating and running a CMake build via the `build/CMakeLists.txt` and also by adding an `include_subdirectory(test)` to the root `CMakeLists.txt` and both are not able to build the project.  
  
I would strongly suggest to remove the `build/CMakeLists.txt` and have only a single (the root) file to build the library. As part of the overall effort to add CMake support to Boost, the `test/CMakeLists.txt` can then focus on defining tests and make use of the [`BoostTest Module](https://github.com/boostorg/cmake/blob/master/include/BoostTest.cmake) instead of trying to come up with a custom solution.  
  
My WIP branch is at https://github.com/Flamefire/test/tree/cmake Currently I tried to support both CMakeLists but that is not feasible.
