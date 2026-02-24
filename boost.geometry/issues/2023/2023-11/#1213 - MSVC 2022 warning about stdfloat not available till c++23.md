# #1213 - MSVC 2022 warning about stdfloat not available till c++23 [Open]

> Username: yoavmil  
> Created at: 2023-11-06 08:48:04 UTC  
> Updated at: 2023-11-06 14:30:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1213  

I get a compiler warning when including `<boost/geometry.hpp>`, because it includes `<promotion.hpp>` which includes `<stdfloat>`  
```c++  
#if __has_include(<stdfloat>) // this resolves to true somehow  
#  include <stdfloat>  
#endif  
```  
  
```c++  
// at stdfloat  
#if !_HAS_CXX23  
_EMIT_STL_WARNING(STL4038, "The contents of <stdfloat> are available only with C++23 or later.");  
#else // ^^^ !_HAS_CXX23 / _HAS_CXX23 vvv  
```  
  
may be replace `#if __has_include(<stdfloat>)` with `#if __has_include(<stdfloat>) & _HAS_CXX23`
