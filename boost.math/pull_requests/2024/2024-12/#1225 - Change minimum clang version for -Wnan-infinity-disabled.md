# #1225 Change minimum clang version for -Wnan-infinity-disabled [Merged]

> Username: mborland  
> Created at: 2024-12-17 11:36:46 UTC  
> Updated at: 2024-12-19 20:48:29 UTC  
> Merged at: 2024-12-19 20:48:25 UTC  
> Closed at: 2024-12-19 20:48:25 UTC  
> Url: https://github.com/boostorg/math/pull/1225  

Fixes breakage introduced in #1222   
  
CC: @ckormanyos

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-12-17 20:40:24 UTC  
> Url: https://github.com/boostorg/math/pull/1225#issuecomment-2549578103  

Hi Matt thank you for looking into this. It appears as though an unrelated slight change in runners is freaking out on our old friend Bernoulli.  
  
Cc: @mborland

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-12-19 20:47:33 UTC  
> Url: https://github.com/boostorg/math/pull/1225#issuecomment-2555744414  

Hi Matt (@mborland) the reason for failing here was addressed in #1226, as discussed there.  
  
So that is fixed now and in develop.  
  
Cc: @jzmaddock

---
