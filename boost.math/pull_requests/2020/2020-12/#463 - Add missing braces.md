# #463 Add missing braces [Merged]

> Username: evanmiller  
> Created at: 2020-12-22 17:19:09 UTC  
> Updated at: 2020-12-30 19:02:54 UTC  
> Merged at: 2020-12-30 19:02:54 UTC  
> Closed at: 2020-12-30 19:02:54 UTC  
> Url: https://github.com/boostorg/math/pull/463  

Clang emits thousands of warnings when running `test_gamma_mp` due to missing braces for subobjects. Add them.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-12-22 19:17:21 UTC  
> Url: https://github.com/boostorg/math/pull/463#issuecomment-749727492  

Thanks, that looks right to me... please nag me to merge once CI has cycled.

---

## Comment 2

> Username: evanmiller  
> Created_at: 2020-12-27 03:36:11 UTC  
> Url: https://github.com/boostorg/math/pull/463#issuecomment-751421807  

@jzmaddock This and #469 are good to go.

---
