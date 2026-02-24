# #475 - Intel intrinsics erroneously included when compiling on Windows with clang-cl for ARM64 [Closed]

> Username: tailsu  
> Created at: 2022-06-14 09:45:19 UTC  
> Updated at: 2023-02-10 18:05:56 UTC  
> Closed at: 2023-02-10 18:05:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/475  

The current MSVC workaround [here](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp#L38-L44) is insufficient to handle the case.  
  
When compiling with clang on Windows, the `immintrin.h` header is available when compiling for ARM64, but `BOOST_MSVC` is not defined, so `BOOST_MP_HAS_IMMINTRIN_H` doesn't get `#undef'd` and compilation fails down the road.  
  
You can reproduce the issue by compiling some demo program with the `clang_cl_arm64_x64` toolset in Visual Studio. You'll need to install clang and clang-cl with the Visual Studio Installer first, of course.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-02-10 18:05:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/475#issuecomment-1426159614  

Fix merged, closing.
