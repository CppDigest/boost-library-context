# #751 - More deprecated warn on denorm_absent [Closed]

> Username: ckormanyos  
> Created at: 2026-02-05 15:07:16 UTC  
> Updated at: 2026-02-06 10:18:15 UTC  
> Closed at: 2026-02-06 10:18:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/751  

In discussion surrounding [this post](https://github.com/boostorg/decimal/pull/1330#issuecomment-3853877797) in the Decimal project, we found another compiler needing un-warning of deprecation of `std::denorm_absent`.  
  
This also needs to be disabled for clang 17 on `__APPLE__` Darwin.  
  
To keep things simple, I'd suggest changing all lines like [this](https://github.com/boostorg/multiprecision/blob/a348cf42b2e108b3ead3f640acaa8ef6965f50dd/include/boost/multiprecision/cpp_dec_float.hpp#L3688)  
  
from OLD  
```  
#ifdef _MSC_VER  
```  
  
to NEW (proposed)  
```  
// Something like  
#if (defined(_MSC_VER) || (defined(__clang__) && (__clang_major__ >= 17)))  
```  
  
I can handle it if/when we agree on the scope of change.  
  
It looks like it's in $13$ places.  
  
Cc: @jzmaddock and @mborland

---

## Comment 1

> Username: mborland  
> Created at: 2026-02-05 15:13:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/751#issuecomment-3854310504  

I think they all need to be:  
  
```c++  
#ifdef _MSC_VER  
#  pragma warning(push)  
#  pragma warning(disable : 4996)  
#elif defined(__clang__) && __clang_major__ >= 17  
#  pragma clang diagnostic push  
#  pragma clang diagnostic ignored "-Wdeprecated-declarations"  
#endif  
  
   static constexpr std::float_denorm_style has_denorm        = std::denorm_absent;  
  
#ifdef _MSC_VER  
#pragma warning(pop)  
#elif defined(__clang__) && __clang_major__ >= 17  
#  pragma clang diagnostic pop  
#endif  
```  
  
And CI should tell us what the lower bound on the clang version is.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2026-02-05 15:26:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/751#issuecomment-3854379545  

Oh of course, there are different dialects for actually disabling the warning.

---

## Comment 3

> Username: mborland  
> Created at: 2026-02-05 15:35:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/751#issuecomment-3854434742  

Microsoft loves being different
