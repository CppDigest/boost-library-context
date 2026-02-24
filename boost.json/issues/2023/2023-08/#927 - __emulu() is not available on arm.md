# #927 - __emulu() is not available on arm [Closed]

> Username: liubing  
> Created at: 2023-08-11 12:59:03 UTC  
> Updated at: 2023-08-17 00:58:35 UTC  
> Closed at: 2023-08-17 00:58:34 UTC  
> Url: https://github.com/boostorg/json/issues/927  

### Version of Boost  
  
boost 1.83beta  
  
### Steps necessary to reproduce the problem  
  
Compile the library on arm64 / windows platform.  
  
### All relevant compiler information  
Error message:  
```  
D:\Projects\pts\pts\include\boost\json\detail\charconv\detail\emulated128.hpp(62): error C3861: '__emulu': identifier not found  
```  
It can be fixed changing    
```  
static inline std::uint64_t umul64(std::uint32_t x, std::uint32_t y) noexcept  
{  
#if defined(BOOST_JSON_HAS_MSVC_32BIT_INTRINSICS)  
    return __emulu(x, y);  
#else  
    return x * static_cast<std::uint64_t>(y);  
#endif  
}  
```  
to  
```  
static inline std::uint64_t umul64(std::uint32_t x, std::uint32_t y) noexcept  
{  
#if defined(BOOST_JSON_HAS_MSVC_32BIT_INTRINSICS) && !defined(_M_ARM)  
    return __emulu(x, y);  
#else  
    return x * static_cast<std::uint64_t>(y);  
#endif  
}  
```

---

## Comment 1

> Username: liubing  
> Created at: 2023-08-16 05:27:29 UTC  
> Url: https://github.com/boostorg/json/issues/927#issuecomment-1679985263  

#928 doesn't really fix this problem. It should be  
```  
#if defined(BOOST_JSON_HAS_MSVC_32BIT_INTRINSICS) && !defined(_M_ARM)  
```  
not  
```  
#if defined(BOOST_JSON_HAS_MSVC_32BIT_INTRINSICS) && !defined(_M_ARM64)  
```

---

## Comment 2

> Username: mborland  
> Created at: 2023-08-16 12:07:16 UTC  
> Url: https://github.com/boostorg/json/issues/927#issuecomment-1680483594  

That's my mistake. The PR has been updated.

---

## Comment 3

> Username: liubing  
> Created at: 2023-08-17 00:58:34 UTC  
> Url: https://github.com/boostorg/json/issues/927#issuecomment-1681453560  

Thank you.
