# #702 Suppress spurious array bound warning from gcc. [Merged]

> Username: jzmaddock  
> Created at: 2025-06-28 18:16:42 UTC  
> Updated at: 2025-06-29 09:01:35 UTC  
> Merged at: 2025-06-29 09:01:35 UTC  
> Closed at: 2025-06-29 09:01:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/702  

Fixes: https://github.com/boostorg/multiprecision/issues/686

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-06-28 18:22:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/702#issuecomment-3015970651  

Hi John, I've often found that suppressing array bounds also needs to be coupled with  
  
```  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wstringop-overflow="  
```  
  
and then the required `pop`. I don't know if this will be the case here, but it woudln't surprise me either.  
  
Cc: @mborland

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-06-29 09:01:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/702#issuecomment-3016436574  

Drone failure is a network issue, circleCI is quickbook failing to build for some strange reason... merging.

---
