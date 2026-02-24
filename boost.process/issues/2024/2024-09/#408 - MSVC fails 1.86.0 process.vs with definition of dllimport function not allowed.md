# #408 - MSVC fails 1.86.0 process.vs with definition of dllimport function not allowed [Closed]

> Username: bebuch  
> Created at: 2024-09-30 08:07:00 UTC  
> Updated at: 2024-12-16 17:08:44 UTC  
> Closed at: 2024-12-13 19:58:17 UTC  
> Url: https://github.com/boostorg/process/issues/408  

```  
boost/process/v2/detail/utf8.hpp(28): error C2491: 'boost::process::v2::detail::conv_string': definition of dllimport function not allowed  
```  
  
In this file are 3 template functions defined as dllimport/dllexport which is invalid.

---

## Comment 1

> Username: Tectu  
> Created at: 2024-12-13 19:31:13 UTC  
> Url: https://github.com/boostorg/process/issues/408#issuecomment-2542120875  

I'm observing the same issue with MSYS2/GCC and boost 1.86.0:  
```  
C:/msys64/mingw64/include/boost/process/v2/detail/utf8.hpp:28:47: error: function 'std::__cxx11::basic_string<_CharT, _Traits, _Allocator> boost::process::v2::detail::conv_string(const CharIn*, std::size_t, Allocator)' definition is marked dllimport  
   28 | std::basic_string<CharOut, Traits, Allocator> conv_string(  
      |                                               ^~~~~~~~~~~  
C:/msys64/mingw64/include/boost/process/v2/detail/utf8.hpp:40:47: error: function 'std::__cxx11::basic_string<_CharT, _Traits, _Allocator> boost::process::v2::detail::conv_string(const wchar_t*, std::size_t, Allocator)' definition is marked dllimport  
   40 | std::basic_string<CharOut, Traits, Allocator> conv_string(  
      |                                               ^~~~~~~~~~~  
C:/msys64/mingw64/include/boost/process/v2/detail/utf8.hpp:64:47: error: function 'std::__cxx11::basic_string<_CharT, _Traits, _Allocator> boost::process::v2::detail::conv_string(const char*, std::size_t, Allocator)' definition is marked dllimport  
   64 | std::basic_string<CharOut, Traits, Allocator> conv_string(  
```

---

## Comment 2

> Username: bebuch  
> Created at: 2024-12-13 19:58:17 UTC  
> Url: https://github.com/boostorg/process/issues/408#issuecomment-2542203283  

This was fixed via #409.

---

## Comment 3

> Username: Tectu  
> Created at: 2024-12-16 17:08:43 UTC  
> Url: https://github.com/boostorg/process/issues/408#issuecomment-2546180857  

I can confirm that boost 1.87.0 no longer suffers from this issue.
