# #481 Various Warning Fixes [Merged]

> Username: mborland  
> Created at: 2022-07-12 13:11:13 UTC  
> Updated at: 2022-07-14 12:47:05 UTC  
> Merged at: 2022-07-14 11:08:39 UTC  
> Closed at: 2022-07-14 11:08:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/481  

Fixes warnings for: Non-ISO (__int128) type usage, 0 as null pointer constant, unused variable, implicit conversion, and ill-formed macro.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-07-12 16:27:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/481#issuecomment-1181985982  

Wow great finds Matt (@mborland). Thank you.

---

## Comment 2

> Username: mborland  
> Created_at: 2022-07-13 12:43:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/481#issuecomment-1183176259  

@ckormanyos or @jzmaddock This is clean on CI and should be good to merge.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-07-13 17:29:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/481#issuecomment-1183493016  

Thank you Matt (@mborland). All these changes are great. I'm really impressed by the quality improvements!  
  
At the moment, I am, however, laying low in waiting for 1.80 to work through release cycle.  
  
I'll follow John's (@jzmaddock) lead on this PR.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-07-14 11:08:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/481#issuecomment-1184313514  

Looks good to me, I don't see any reason not to merge, though this won't make master till 1.81 now I guess.

---
