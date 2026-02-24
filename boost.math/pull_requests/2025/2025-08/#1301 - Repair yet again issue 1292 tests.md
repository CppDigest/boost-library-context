# #1301 Repair yet again issue 1292 tests [Merged]

> Username: ckormanyos  
> Created at: 2025-08-16 09:22:07 UTC  
> Updated at: 2025-08-16 18:00:07 UTC  
> Merged at: 2025-08-16 11:47:16 UTC  
> Closed at: 2025-08-16 11:47:16 UTC  
> Url: https://github.com/boostorg/math/pull/1301  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-08-16 10:58:20 UTC  
> Updated_at: 2025-08-16 10:58:36 UTC  
> Url: https://github.com/boostorg/math/pull/1301#issuecomment-3193585277  

Hi John (@jzmaddock) and Matt (@mborland) while I'm struggling with some ad-hoc tests in Math/Multiprecision mixture (on Git issue 1292), I see what might be an unrelated problem.  
  
On drone there is an actual [failure in interpolators](https://drone.cpp.al/boostorg/math/2330/13/2) that I can't explain. Is it real or bogus?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-08-16 12:13:52 UTC  
> Url: https://github.com/boostorg/math/pull/1301#issuecomment-3193629982  

I know nothing about that test case, but superficially, it looks to me as if we're requiring a zero result, and are instead getting back 1eps.  Assuming the test can only produce a low absolute error, then this should probably be allowed.  Probably ;)

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-08-16 12:47:25 UTC  
> Url: https://github.com/boostorg/math/pull/1301#issuecomment-3193649425  

> I know nothing about that test case, but superficially, it looks to me as if we're requiring a zero result, and are instead getting back 1eps.  
  
Me neither. I merged my changes anyway, figuring this interpolator issue is unrelated.  
  
> Assuming the test can only produce a low absolute error, then this should probably be allowed. Probably  
  
Maybe Matt know something next week? Otherwise, I can take a deeper look into it.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2025-08-16 17:46:12 UTC  
> Url: https://github.com/boostorg/math/pull/1301#issuecomment-3193815058  

@ckormanyos : My apologies-that test can _definitely_ removed. In particular, this is the comment above it:  
  
https://github.com/boostorg/math/blob/7535ee105b3e42462f9bc1879a6b8bcbbe79c22b/test/jso_test.cpp#L55  
  
However, looking at the implementation of the `ackley` function, it does appear that floating point error can make non-exact solutions be _lower_ than the true solution.

---
