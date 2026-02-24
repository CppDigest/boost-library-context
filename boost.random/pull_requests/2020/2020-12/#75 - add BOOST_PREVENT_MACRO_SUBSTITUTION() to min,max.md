# #75  add BOOST_PREVENT_MACRO_SUBSTITUTION() to min,max [Closed]

> Username: kotika  
> Created at: 2020-12-23 18:19:46 UTC  
> Updated at: 2020-12-23 20:19:18 UTC  
> Closed at: 2020-12-23 20:19:05 UTC  
> Url: https://github.com/boostorg/random/pull/75  

Fix https://github.com/boostorg/math/issues/468, which is in turn caused by the collision in minwindef.h on Windows.h

---

## Comment 1

> Username: evanmiller  
> Created_at: 2020-12-23 18:23:51 UTC  
> Url: https://github.com/boostorg/random/pull/75#issuecomment-750419299  

My understanding is that `BOOST_PREVENT_MACRO_SUBSTITUTION` should be between `min` and `()`, i.e.  
  
```  
BOOST_STATIC_CONSTEXPR result_type min BOOST_PREVENT_MACRO_SUBSTITUTION () {return mixmax_min;}  
```  
  
Instead of  
  
```  
BOOST_STATIC_CONSTEXPR result_type min() BOOST_PREVENT_MACRO_SUBSTITUTION() {return mixmax_min;}  
```

---

## Comment 2

> Username: evanmiller  
> Created_at: 2020-12-23 18:25:19 UTC  
> Url: https://github.com/boostorg/random/pull/75#issuecomment-750419825  

Usage example  
  
https://www.boost.org/doc/libs/1_75_0/boost/limits.hpp

---

## Comment 3

> Username: kotika  
> Created_at: 2020-12-23 20:19:18 UTC  
> Url: https://github.com/boostorg/random/pull/75#issuecomment-750457937  

Superceded.

---
