# #455 Fix issues with test/extension/io/tiff_test.cpp build and run [Merged]

> Username: mloskot  
> Created at: 2020-03-17 20:30:13 UTC  
> Updated at: 2020-03-20 09:21:29 UTC  
> Merged at: 2020-03-20 09:21:26 UTC  
> Closed at: 2020-03-20 09:21:26 UTC  
> Url: https://github.com/boostorg/gil/pull/455  

### Description  
  
- IO: Add missing `rgba8_image_t` to `any_image` for tiff to prevent unexpected failure. The `test/extension/io/images/tiff/test.tif` is RGBA  
- CMake: Fix look-up for TIFF add-in library for C++ (tiffxx)  
  
----  
  
The CMake changes should be submitted to CMake, for https://gitlab.kitware.com/cmake/cmake/-/blob/master/Modules/FindTIFF.cmake  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass

---
