# #797 Fix right endpoint derivative estimate error in cardinal cubic b spline [Merged]

> Username: sjrowlinson  
> Created at: 2022-07-10 15:10:53 UTC  
> Updated at: 2022-07-11 00:40:31 UTC  
> Merged at: 2022-07-10 20:14:41 UTC  
> Closed at: 2022-07-10 20:14:41 UTC  
> Url: https://github.com/boostorg/math/pull/797  

There is a bug in the current right endpoint derivative calculation for the `cardinal_cubic_b_spline` class, which this PR should now fix. The issue was that the signs of the forward finite difference coeffs were not flipped when applying to backwards finite difference at right endpoint, and these coefficients were incorrectly applied in reverse order.  
  
I also added a test to verify these changes, based on the use-case (interpolation of cross-section of spherical conic section) where I first noticed this bug.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-07-10 15:22:48 UTC  
> Url: https://github.com/boostorg/math/pull/797#issuecomment-1179748100  

@NAThompson I approved the CI run if you want to take a look at this.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2022-07-10 16:45:31 UTC  
> Url: https://github.com/boostorg/math/pull/797#issuecomment-1179761282  

@sjrowlinson : Thanks! Sorry for what I'm sure was surely a totally baffling debug session for you.

---

## Comment 3

> Username: sjrowlinson  
> Created_at: 2022-07-10 19:33:45 UTC  
> Url: https://github.com/boostorg/math/pull/797#issuecomment-1179785378  

> @sjrowlinson : Thanks! Sorry for what I'm sure was surely a totally baffling debug session for you.  
  
No worries! It confounded me for a couple of days, but thankfully the source of the error wasn't too tricky to track down in the end.

---

## Comment 4

> Username: mborland  
> Created_at: 2022-07-10 20:14:01 UTC  
> Url: https://github.com/boostorg/math/pull/797#issuecomment-1179791504  

The CI failures are 1x fail to clone and 1x spurious failure in `naive_monte_carlo` with my favorite error message:  
  
```  
====== BEGIN OUTPUT ======  
1009s  
2272	Running 1 test case...  
1009s  
2273	Default hardware concurrency = 2  
1009s  
2274	terminate called after throwing an instance of 'std::domain_error'  
1009s  
2275	  what():  You have done something wrong.  
1009s  
2276	  
1009s  
2277	Testing that a reasonable action is performed by the Monte-Carlo integrator when the integrand throws an exception on type float  
1009s  
2278	unknown location(0): fatal error: in "naive_monte_carlo_test": signal: SIGABRT (application abort requested)  
1009s  
2279	naive_monte_carlo_test.cpp(451): last checkpoint: "naive_monte_carlo_test" test entry  
1009s  
2280	  
1009s  
2281	*** 1 failure is detected in the test module "naive_monte_carlo_test"  
1009s  
2282	  
1009s  
2283	EXIT STATUS: 201  
1009s  
2284	  
====== END OUTPUT ======  
```

---

## Comment 5

> Username: NAThompson  
> Created_at: 2022-07-11 00:40:31 UTC  
> Url: https://github.com/boostorg/math/pull/797#issuecomment-1179839835  

yeah. . . maybe I shouldn't have written that error message . . .

---
