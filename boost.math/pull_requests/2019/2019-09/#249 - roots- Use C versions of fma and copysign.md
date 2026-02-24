# #249 roots: Use C versions of fma and copysign [Closed]

> Username: neheb  
> Created at: 2019-09-06 02:37:22 UTC  
> Updated at: 2019-09-14 16:03:24 UTC  
> Closed at: 2019-09-14 16:03:24 UTC  
> Url: https://github.com/boostorg/math/pull/249  

fma and copysign are C++11 functions that do not get included in std::  
by uClibc-ng. Switch to the C version.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-09-06 09:20:50 UTC  
> Url: https://github.com/boostorg/math/pull/249#issuecomment-528780935  

That would break long double support.  
  
Am I correct in thinking that you're using gcc + libstdc++  ?  If so can you tell me what happens if you compile:  
  
```  
#include <cmath>  
  
#ifdef _GLIBCXX_USE_C99_MATH_TR1  
#error 1  
#else  
#error 2  
#endif  
```  
?

---

## Comment 2

> Username: neheb  
> Created_at: 2019-09-06 20:09:02 UTC  
> Url: https://github.com/boostorg/math/pull/249#issuecomment-528996935  

error 2.  
  
Local Ubuntu compile gives error 1.

---

## Comment 3

> Username: neheb  
> Created_at: 2019-09-06 20:13:03 UTC  
> Url: https://github.com/boostorg/math/pull/249#issuecomment-528998235  

This is the solution in a different project: https://github.com/facebook/folly/blob/82a534256b216e9c12852a09e3d934bd76ec1d18/folly/portability/Math.h#L32  
  
My C++ is not that great though.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-09-12 08:04:31 UTC  
> Url: https://github.com/boostorg/math/pull/249#issuecomment-530714258  

I realise it's tied up with other changes, but can you check that the changes to roots.hpp in https://github.com/boostorg/math/pull/251 fix this issue for you?  Thanks!

---
