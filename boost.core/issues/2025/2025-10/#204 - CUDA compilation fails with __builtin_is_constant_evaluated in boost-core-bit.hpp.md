# #204 - CUDA compilation fails with __builtin_is_constant_evaluated in boost/core/bit.hpp [Open]

> Username: Michael-Preisel  
> Created at: 2025-10-07 15:20:19 UTC  
> Updated at: 2025-10-07 15:20:19 UTC  
> Url: https://github.com/boostorg/core/issues/204  

**Description**:   
- Boost Version: 1.89.0  
- CUDA Version: 12.6  
- MSVC Version: 2022 (1925+)  
- Error: "variable in constexpr function is uninitialized" for unsigned long r;  
  
**Root Cause**:   
Line 35 enables BOOST_CORE_HAS_BUILTIN_ISCONSTEVAL for MSVC 1925+ but doesn't exclude CUDA compiler (nvcc), which has stricter constexpr requirements.  
  
**Proposed Fix**:  
Change line 42 from:  
#if defined(BOOST_MSVC) && BOOST_MSVC >= 1925  
to:  
#if defined(BOOST_MSVC) && BOOST_MSVC >= 1925 && '!defined(\_\_CUDACC\_\_)'  
  
**Impact**: Prevents CUDA compilation errors while maintaining optimization for regular MSVC builds.
