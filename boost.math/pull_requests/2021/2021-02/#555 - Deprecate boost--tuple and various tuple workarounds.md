# #555 Deprecate boost::tuple and various tuple workarounds. [Merged]

> Username: NAThompson  
> Created at: 2021-02-24 21:07:05 UTC  
> Updated at: 2021-02-25 17:40:57 UTC  
> Merged at: 2021-02-25 14:15:17 UTC  
> Closed at: 2021-02-25 14:15:17 UTC  
> Url: https://github.com/boostorg/math/pull/555  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-02-25 14:15:06 UTC  
> Url: https://github.com/boostorg/math/pull/555#issuecomment-785925760  

The error is quite mind boggling:  
  
```  
testing.capture-output ../../../bin.v2/libs/math/test/naive_monte_carlo_test_5.test/clang-linux-9.0.1/debug/link-static/threading-multi/visibility-hidden/naive_monte_carlo_test_5.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Default hardware concurrency = 2  
Testing that a reasonable action is performed by the Monte-Carlo integrator when the integrand throws an exception on type float  
unknown location(0): fatal error: in "naive_monte_carlo_test": std::domain_error: You have done something wrong.  
  
naive_monte_carlo_test.cpp(451): last checkpoint: "naive_monte_carlo_test" test entry  
  
*** 1 failure is detected in the test module "naive_monte_carlo_test"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
But it appears to have nothing to do with this PR.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-02-25 17:06:38 UTC  
> Url: https://github.com/boostorg/math/pull/555#issuecomment-786056590  

I realise it's a bigger change, but shouldn't we remove tools/tuple.hpp altogether and use std::tuples directly?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-02-25 17:13:40 UTC  
> Url: https://github.com/boostorg/math/pull/555#issuecomment-786061561  

@jzmaddock : Yes, I was wanting to do that, however, I didn't want to take that one in one bite because I figured I'd get myself into trouble.

---

## Comment 4

> Username: pabristow  
> Created_at: 2021-02-25 17:40:56 UTC  
> Url: https://github.com/boostorg/math/pull/555#issuecomment-786079796  

As Ryan Air CEO might say, isn't removing /tools/tuple.hpp also "unnecessarily pissing-off the customers?"  Won't it break existing customers working code and force them to update to use std::tuple?  
  
(Nothing to stop using replace all Boost.Math's and Boost.Multiprecision's use of math's own tuple with std::tuple, of course).

---
