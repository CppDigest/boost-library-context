# #874 - Please do not set CMAKE_MODULE_PATH. [Open]

> Username: qis  
> Created at: 2022-06-19 01:24:16 UTC  
> Updated at: 2022-06-19 01:24:16 UTC  
> Url: https://github.com/boostorg/compute/issues/874  

https://github.com/boostorg/compute/blob/36350b7/CMakeLists.txt#L64 sets `CMAKE_MODULE_PATH` and overwrites CMake and possibly toolchain and Compiler/Platform settings.  
  
This prevents users from providing `find_package` scripts or configs that use `include(...` relative to the default or toolchain `CMAKE_MODULE_PATH`.  
  
Please replace it with   
  
```cmake  
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")  
```  
  
like the `hof` library does, or  
  
```cmake  
list(INSERT CMAKE_MODULE_PATH 0 ${CMAKE_BINARY_DIR}/cmake)  
```  
  
like the `gil` does.
