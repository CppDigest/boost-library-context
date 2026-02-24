# #1017 MSVC 14_0 stdndards and has-include corrections [Merged]

> Username: ckormanyos  
> Created at: 2023-08-22 09:09:54 UTC  
> Updated at: 2023-08-22 14:50:57 UTC  
> Merged at: 2023-08-22 14:50:52 UTC  
> Closed at: 2023-08-22 14:50:52 UTC  
> Url: https://github.com/boostorg/math/pull/1017  

The purpose of this PR is to handle trivial syntax improvements mentioned in #1016.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-08-22 09:25:07 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1687812938  

The lexical parser isn't into it.  
  
```  
fatal error C1012: unmatched parenthesis: missing ')'  
```  
  
I need to go with the former preprocessor stuff, or at least try it out first.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2023-08-22 10:08:09 UTC  
> Updated_at: 2023-08-22 10:08:27 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1687893061  

Well the code logic is OK and things are going green but I messed up the GHA logic a bit and a few too many runners are being run. So this will need one more logic fix on the YAML layer. Coming right up, ...

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-08-22 11:22:58 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1687998256  

>The lexical parser isn't into it.  
  
It does work: Boost.Config is using it for all compilers including vintage msvc: https://github.com/boostorg/config/blob/d483059795f8bf77120ee423d1660532223e5795/include/boost/config/detail/select_stdlib_config.hpp#L18

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2023-08-22 12:43:51 UTC  
> Updated_at: 2023-08-22 12:44:45 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1688113968  

>> The lexical parser isn't into it.  
  
> Boost.Config is using it for all ...  
  
Ahhh good save, John. I tried the line:  
  
```cpp  
#if (defined(__has_include) && (__has_include(<stdfloat>)))  
```  
  
But the variation should be:  
  
```cpp  
#if defined(__has_include)  
#if __has_include(<stdfloat>)  
#include <stdfloat>  
#endif  
#endif  
```

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2023-08-22 12:45:47 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1688116858  

> good save, John  
  
John (@jzmaddock) could we agree on?  
  
```cpp  
#if defined(__has_include)  
#if __has_include(<stdfloat>)  
#include <stdfloat>  
#endif  
#endif  
```  
  
Cc: @mborland

---

## Comment 6

> Username: mborland  
> Created_at: 2023-08-22 12:49:51 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1688123241  

> ```c++  
> #if defined(__has_include)  
> #if __has_include(<stdfloat>)  
> #include <stdfloat>  
> #endif  
> #endif  
> ```  
  
That is the format that GCC recommends: https://gcc.gnu.org/onlinedocs/cpp/_005f_005fhas_005finclude.html

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2023-08-22 13:05:00 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1688150186  

> the format that GCC recommends  
  
Great thank you Matt (@mborland). I will cycle this through, then:  
  
```cpp  
#if defined __has_include  
#  if __has_include (<stdfloat>)  
#    include <stdfloat>  
#  endif  
#endif  
```

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2023-08-22 13:16:15 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1688170061  

Matt (@mborland) while we are at it, I used super-rudimentary logic on the YAML-ing. Would you please take a look and see if you can agree to that? Or can we do better?

---

## Comment 9

> Username: mborland  
> Created_at: 2023-08-22 13:19:05 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1688174643  

> Matt (@mborland) while we are at it, I used super-rudimentary logic on the YAML-ing. Would you please take a look and see if you can agree to that? Or can we do better?  
  
It looks good to me. Definitely more compact than making it own config for one compiler.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2023-08-22 13:22:22 UTC  
> Updated_at: 2023-08-22 13:22:57 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1688180163  

> It looks good to me.  
  
Thanks Matt. OK let's let this thing cycle through. It is cycling green even with recent changes in develop to `denorm_min` in Multiprecision. These are in Multiprecision's develop now.  
  
I'm not sure if this is or is not expected. But I'm happy to observe that.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2023-08-22 14:50:21 UTC  
> Url: https://github.com/boostorg/math/pull/1017#issuecomment-1688341377  

Single failure on drone to clone. Merging.

---
