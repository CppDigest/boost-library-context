# #77 Fix issue #76, use intrinsics on x86, ARM & ARM64 [Closed]

> Username: wyattoday  
> Created at: 2019-05-31 13:44:18 UTC  
> Updated at: 2019-05-31 15:21:34 UTC  
> Closed at: 2019-05-31 14:35:52 UTC  
> Url: https://github.com/boostorg/winapi/pull/77  



---

## Comment 1

> Username: Lastique  
> Created_at: 2019-05-31 14:15:54 UTC  
> Url: https://github.com/boostorg/winapi/pull/77#issuecomment-497724407  

I don't think this is correct wrt. `_M_ARM` and `_M_IX86`. As I remember, at least some MSVC versions don't provide pointer intrinsics on 32-bit targets.

---

## Comment 2

> Username: wyattoday  
> Created_at: 2019-05-31 14:55:06 UTC  
> Url: https://github.com/boostorg/winapi/pull/77#issuecomment-497739036  

It's been available for x86 since at least Visual Studio 2015. So, perhaps modify the #ifdef to include that:  
  
```  
|| (BOOST_MSVC >= 1900 && defined(_M_IX86))  
```  
  
Also, as far as I can see for every MSVC that supports targeting ARM (32-bit arm), that intrinsic has been available. So, that part of the ifdef shouldn't have been chopped out.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-05-31 14:59:25 UTC  
> Url: https://github.com/boostorg/winapi/pull/77#issuecomment-497740663  

> Also, as far as I can see for every MSVC that supports targeting ARM (32-bit arm), that intrinsic has been available.  
  
intrin.h in MSVC-10 contains checks for `_M_ARM` but doesn't define `_InterlockedCompareExchangePointer` for that platform.

---

## Comment 4

> Username: wyattoday  
> Created_at: 2019-05-31 15:07:19 UTC  
> Url: https://github.com/boostorg/winapi/pull/77#issuecomment-497743600  

Well, MSDN documentation doesn't explicitly say when they made it available, so I'd be happy with a VS 2015 check for both x86 / ARM:  
  
```  
|| (BOOST_MSVC >= 1900 && (defined(_M_IX86) || defined(_M_ARM)))  
```  
  
People using modern compilers will get the benefit of the intrinsics.

---

## Comment 5

> Username: Lastique  
> Created_at: 2019-05-31 15:21:34 UTC  
> Url: https://github.com/boostorg/winapi/pull/77#issuecomment-497748997  

To be clear, there is no benefit from using pointer intrinsics compared to the properly sized integer intrinsics. I'll add compiler version checks, though.

---
