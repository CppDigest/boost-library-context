# #674 Constexpr isfinite and isnormal [Merged]

> Username: mborland  
> Created at: 2021-08-17 21:11:15 UTC  
> Updated at: 2021-08-24 17:24:43 UTC  
> Merged at: 2021-08-24 04:47:41 UTC  
> Closed at: 2021-08-24 04:47:41 UTC  
> Url: https://github.com/boostorg/math/pull/674  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-08-22 08:22:28 UTC  
> Url: https://github.com/boostorg/math/pull/674#issuecomment-903232522  

@NAThompson and @jzmaddock This should be good for review. The only CI failure is from `naive_monte_carlo_test`:  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Default hardware concurrency = 2  
terminate called after throwing an instance of 'std::domain_error'  
  what():  You have done something wrong.  
  
Testing that a reasonable action is performed by the Monte-Carlo integrator when the integrand throws an exception on type float  
unknown location(0): fatal error: in "naive_monte_carlo_test": signal: SIGABRT (application abort requested)  
naive_monte_carlo_test.cpp(451): last checkpoint: "naive_monte_carlo_test" test entry  
  
*** 1 failure is detected in the test module "naive_monte_carlo_test"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
I thought that error had been fixed since I haven't seen it in quite a while now.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-08-23 04:15:46 UTC  
> Updated_at: 2021-08-23 04:15:56 UTC  
> Url: https://github.com/boostorg/math/pull/674#issuecomment-903429181  

I just read that error message, thinking "wow, that is the least helpful error message in the universe".  
  
And I realized only I would right something like that.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-08-23 04:16:47 UTC  
> Updated_at: 2021-08-23 04:17:09 UTC  
> Url: https://github.com/boostorg/math/pull/674#issuecomment-903429630  

I'm happy with this code, BTW.  
  
If @jzmaddock doesn't have time to look at it I'll merge it.

---
