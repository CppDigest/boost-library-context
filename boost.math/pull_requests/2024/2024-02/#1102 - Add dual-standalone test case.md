# #1102 Add dual-standalone test case [Merged]

> Username: mborland  
> Created at: 2024-02-23 07:23:49 UTC  
> Updated at: 2024-02-26 07:16:57 UTC  
> Merged at: 2024-02-26 07:16:54 UTC  
> Closed at: 2024-02-26 07:16:54 UTC  
> Url: https://github.com/boostorg/math/pull/1102  

@ckormanyos Locally I am not seeing any errors/warnings from this test case. Do you have an example as reported here: https://github.com/boostorg/math/pull/1100#issuecomment-1960805834?

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-02-23 10:20:21 UTC  
> Url: https://github.com/boostorg/math/pull/1102#issuecomment-1961066431  

Hi Matt, great question. I am away from office at this moment. I have a test case running on GCC-arm-none-eabi exhibiting this. I’ll produce that link for you tonight

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-02-24 10:18:31 UTC  
> Url: https://github.com/boostorg/math/pull/1102#issuecomment-1962319985  

Hi Matt (@mborland) there seems to be no issue and everything is OK in my standalone metal benchmarks. I did, however, need to use the macros properly, which had not previously been the case. See also notes in #1100 in the post [here](https://github.com/boostorg/math/pull/1100#issuecomment-1962319460).

---
