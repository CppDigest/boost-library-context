# #1343 - autodiff.hpp and autodiff_reverse.hpp share same macro [Closed]

> Username: xuruilong100  
> Created at: 2025-12-19 01:47:23 UTC  
> Updated at: 2025-12-19 12:35:38 UTC  
> Closed at: 2025-12-19 12:35:38 UTC  
> Url: https://github.com/boostorg/math/issues/1343  

In Boost 1.90, `autodiff.hpp` and `autodiff_reverse.hpp` share same macro  
  
```cpp  
#ifndef BOOST_MATH_DIFFERENTIATION_AUTODIFF_HPP  
#define BOOST_MATH_DIFFERENTIATION_AUTODIFF_HPP  
```  
  
So, I can't include them in same file. Is it a bug? Or, is it a feature?

---

## Comment 1

> Username: demroz  
> Created at: 2025-12-19 07:34:35 UTC  
> Url: https://github.com/boostorg/math/issues/1343#issuecomment-3673933693  

This is a bug. I'll update it and push a pr
