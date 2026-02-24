# #476 explicitly check for ARM/ARM64 on Windows [Merged]

> Username: tailsu  
> Created at: 2022-06-14 10:06:46 UTC  
> Updated at: 2022-07-03 11:13:47 UTC  
> Merged at: 2022-07-03 11:10:54 UTC  
> Closed at: 2022-07-03 11:10:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/476  

Tentative fix for https://github.com/boostorg/multiprecision/issues/475

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-06-14 11:57:29 UTC  
> Updated_at: 2022-06-14 11:58:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/476#issuecomment-1155086384  

Thanks for looking into this @tailsu.  
  
I have approved the CI run (for first-time contributors).  
  
There was some similar discussion a few months ago, so we should also consider to ask @jzmaddock if this is a similar case and if the fix is OK.

---

## Comment 2

> Username: tailsu  
> Created_at: 2022-06-14 13:57:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/476#issuecomment-1155223324  

OK, I'm glad 👍   
  
It would also make sense to add the compiler/platform combo to CI but I'm not sure how to do that.

---

## Comment 3

> Username: mborland  
> Created_at: 2022-07-02 18:45:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/476#issuecomment-1172943605  

@jzmaddock and @ckormanyos this should probably make the release as well since it is linked to multiple issues now.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-07-03 11:13:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/476#issuecomment-1173062005  

Thanks @tailsu, John and Matt for driving this forward.

---
