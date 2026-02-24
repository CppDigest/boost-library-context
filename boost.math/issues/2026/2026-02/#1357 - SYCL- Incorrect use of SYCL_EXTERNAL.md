# #1357 - SYCL: Incorrect use of SYCL_EXTERNAL [Closed]

> Username: illuhad  
> Created at: 2026-02-05 19:42:02 UTC  
> Updated at: 2026-02-05 20:37:49 UTC  
> Closed at: 2026-02-05 20:37:49 UTC  
> Url: https://github.com/boostorg/math/issues/1357  

Hi!  
  
boost.math uses the `SYCL_EXTERNAL` macro on certain functions, as a potential value of `BOOST_MATH_GPU_ENABLED` when compiling with a SYCL compiler.  
  
The positioning of the macro currently is however incorrect, which causes compilation failure with SYCL implementations which implement `SYCL_EXTERNAL` as a C++11-style attribute (`[[attrib]]`) and not a GNU-style attribute (`__attribute__((attrib))`). Note that different SYCL implementations follow different implementation strategies:  
  
- DPC++ uses GNU-style attributes  
- AdaptiveCpp use C++11 attributes (error report due to this issue when building boost: https://github.com/AdaptiveCpp/AdaptiveCpp/issues/1957)  
- Upstream LLVM SYCL compiler which is currently in development uses C++11 attributes  
  
The SYCL specification was a little unclear here; we have discussed the issue in the Khronos SYCL working group and it was decided that the intent in the specification is that the `SYCL_EXTERNAL` macro should be only valid before the declaration in those places where both GNU-style and C++11-style attributes work, i.e. both implementation options are valid, and client code should only use that subset of attribute positions that work in both cases:  
See: https://github.com/KhronosGroup/SYCL-Docs/issues/956  
  
In practice this means that, if boost.math desires SYCL compliance and portability between implementations, current patterns in boost.math like  
```  
// Not an allowed position for C++11 attributes  
inline BOOST_MATH_GPU_ENABLED void f();  
```  
should be changed to  
```  
BOOST_MATH_GPU_ENABLED inline void f();  
```  
where `BOOST_MATH_GPU_ENABLED` is mapped to `SYCL_EXTERNAL`.  
  
This works with both C++ and GNU-style attribute implementations of `SYCL_EXTERNAL`. The suggested position is also supported by `nvcc` for the `__device__` attribute, so `BOOST_MATH_GPU_ENABLED` could still be mapped to either `SYCL_EXTERNAL` or CUDA attributes,  depending on compiler.

---

## Comment 1

> Username: mborland  
> Created at: 2026-02-05 20:32:50 UTC  
> Url: https://github.com/boostorg/math/issues/1357#issuecomment-3856042062  

I see in your linked issue you are trying to use Boost 1.87. Tracking on current develop your error location had been changed to:  
  
```c++  
template<> BOOST_MATH_GPU_ENABLED inline int (fpclassify)(double t)  
```  
  
I don't see any instances of functions/classes/etc in the incorrect form you described.  
  
There are a few with repeated annotations that I will clean up:  
  
```c++  
BOOST_MATH_GPU_ENABLED BOOST_MATH_GPU_ENABLED inline ...  
```

---

## Comment 2

> Username: mborland  
> Created at: 2026-02-05 20:37:49 UTC  
> Url: https://github.com/boostorg/math/issues/1357#issuecomment-3856062158  

Yes, I believe this is a duplicate of #1270 which was fixed in June 2025 with #1271. Please re-open if you still have issues after attempting a newer boost version.
