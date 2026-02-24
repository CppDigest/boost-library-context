# #157 - error C2039: "_finite": 不是"std"的成员 [Closed]

> Username: liuaifu  
> Created at: 2023-09-15 02:16:21 UTC  
> Updated at: 2023-11-20 14:18:20 UTC  
> Closed at: 2023-11-20 14:18:20 UTC  
> Url: https://github.com/boostorg/core/issues/157  

boost/core/cmath.hpp(239,12): error C2039: "_finite": 不是"std"的成员  
  
boost/core/cmath.hpp(239,12): error C2039: "_finite": is not a member of "std"  
  
Compiled OK after comment "using std::isfinite;"  
  
Windows10 + Boost1.83 + Visual C++ 17.5.2  
/std:c++14

---

## Comment 1

> Username: pdimov  
> Created at: 2023-09-15 09:49:18 UTC  
> Url: https://github.com/boostorg/core/issues/157#issuecomment-1720993751  

You have a `#define isfinite _finite` somewhere.

---

## Comment 2

> Username: liuaifu  
> Created at: 2023-11-20 14:18:20 UTC  
> Url: https://github.com/boostorg/core/issues/157#issuecomment-1819152552  

This issue has been resolved. It took a while, and I forgot what caused it, but it should be my own mistake anyway.
