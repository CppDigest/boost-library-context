# #965 Conditionally Disable MP Testing  [Merged]

> Username: mborland  
> Created at: 2023-03-06 17:31:38 UTC  
> Updated at: 2023-03-07 21:09:49 UTC  
> Merged at: 2023-03-07 19:09:10 UTC  
> Closed at: 2023-03-07 19:09:10 UTC  
> Url: https://github.com/boostorg/math/pull/965  

Should significantly reduce resource usage. Changes a macro used for testing under standalone so now you have to explicitly enable MP testing.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-03-06 17:56:59 UTC  
> Url: https://github.com/boostorg/math/pull/965#issuecomment-1456651382  

Could we enable MP-testing for at least ONE compiler in CI?  Otherwise I fear breakages will go unnoticed.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-03-06 18:01:53 UTC  
> Url: https://github.com/boostorg/math/pull/965#issuecomment-1456661307  

> Could we enable MP-testing for at least ONE compiler in CI? Otherwise I fear breakages will go unnoticed.  
  
Yes. Once the drone run finishes I will see what the delta in our resource usage is. If we need to whack testing down more I can continue to sprinkle that macro around, and then define it in a few CI runs.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-03-06 18:22:10 UTC  
> Url: https://github.com/boostorg/math/pull/965#issuecomment-1456711830  

> Yes. Once the drone run finishes I will see what the delta in our resource usage is. If we need to whack testing down more I can continue to sprinkle that macro around, and then define it in a few CI runs.  
  
Got it, cool.  I suspect the multiprc_concept_check_N tests are probably the worst offenders for memory usage.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-03-06 18:27:31 UTC  
> Updated_at: 2023-03-06 18:27:44 UTC  
> Url: https://github.com/boostorg/math/pull/965#issuecomment-1456722061  

> > Yes. Once the drone run finishes I will see what the delta in our resource usage is. If we need to whack testing down more I can continue to sprinkle that macro around, and then define it in a few CI runs.  
>   
> Got it, cool. I suspect the multiprc_concept_check_N tests are probably the worst offenders for memory usage.  
  
Those do look aggressive. The changes to quadrature dropped the CI run time for that test suite by ~20%.

---

## Comment 5

> Username: mborland  
> Created_at: 2023-03-06 20:25:41 UTC  
> Url: https://github.com/boostorg/math/pull/965#issuecomment-1456924168  

@jzmaddock  I appreciate your comment in `test_1F1` that: `These next 2 tests take effectively "forever" to compile with clang:`

---

## Comment 6

> Username: mborland  
> Created_at: 2023-03-07 19:08:01 UTC  
> Url: https://github.com/boostorg/math/pull/965#issuecomment-1458683479  

The two CI failures in drone are spurious.

---
