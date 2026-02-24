# #1056 Improve accuracy of quartic_roots [Merged]

> Username: NAThompson  
> Created at: 2023-11-27 02:39:50 UTC  
> Updated at: 2023-11-27 16:53:10 UTC  
> Merged at: 2023-11-27 16:52:57 UTC  
> Closed at: 2023-11-27 16:52:57 UTC  
> Url: https://github.com/boostorg/math/pull/1056  

Previously, we took a square root, and then squared it later in the computation. In Issue #1055, we observed that this caused enough loss of accuracy to prevent finding real roots.  
  
Use the original value, instead of squaring the square root.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2023-11-27 10:39:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1056#pullrequestreview-1750126790  

This looks good to me. The only error in CI is a personal favorite:  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Default hardware concurrency = 2  
terminate called after throwing an instance of 'std::domain_error'  
  what():  You have done something wrong.  
  
Testing that a reasonable action is performed by the Monte-Carlo integrator when the integrand throws an exception on type float  
unknown location(0): fatal error: in "naive_monte_carlo_test": signal: SIGABRT (application abort requested)  
naive_monte_carlo_test.cpp(457): last checkpoint: "naive_monte_carlo_test" test entry  
  
*** 1 failure is detected in the test module "naive_monte_carlo_test"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Comment 2

> Username: NAThompson  
> Created_at: 2023-11-27 16:33:54 UTC  
> Url: https://github.com/boostorg/math/pull/1056#issuecomment-1828189353  

@mborland : Someday I'll fix that one . . .   
  
@wztzjhn: Fixed on develop.

---
