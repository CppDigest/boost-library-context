# #926 - _umul128() is not available on arm64 [Closed]

> Username: liubing  
> Created at: 2023-08-11 12:56:28 UTC  
> Updated at: 2023-08-16 05:21:46 UTC  
> Closed at: 2023-08-16 05:21:46 UTC  
> Url: https://github.com/boostorg/json/issues/926  

### Version of Boost  
  
boost 1.83beta  
  
### Steps necessary to reproduce the problem  
  
Compile the library on arm64 / windows platform.  
  
### All relevant compiler information  
Error message:  
```  
D:\Projects\pts\pts\include\boost\json\detail\charconv\detail\emulated128.hpp(79): error C3861: '_umul128': identifier not found  
```  
It can be fixed changing    
```  
    #elif defined(BOOST_JSON_HAS_MSVC_64BIT_INTRINSICS)  
  
    std::uint64_t high;  
    std::uint64_t low = _umul128(x, y, &high);  
    return {high, low};  
  
    // https://developer.arm.com/documentation/dui0802/a/A64-General-Instructions/UMULH  
    #elif defined(__arm__)  
```  
to  
```  
    #elif defined(BOOST_JSON_HAS_MSVC_64BIT_INTRINSICS) && !defined(_M_ARM64)  
  
    std::uint64_t high;  
    std::uint64_t low = _umul128(x, y, &high);  
    return {high, low};  
  
    // https://developer.arm.com/documentation/dui0802/a/A64-General-Instructions/UMULH  
    #elif defined(__arm__)  
  
```
