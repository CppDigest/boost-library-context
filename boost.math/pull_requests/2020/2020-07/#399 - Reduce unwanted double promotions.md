# #399 Reduce unwanted double promotions [Merged]

> Username: evanmiller  
> Created at: 2020-07-09 16:57:34 UTC  
> Updated at: 2020-07-13 19:21:18 UTC  
> Merged at: 2020-07-13 19:03:40 UTC  
> Closed at: 2020-07-13 19:03:40 UTC  
> Url: https://github.com/boostorg/math/pull/399  

A small audit of the Bessel and beta functions revealed a number of places where primitive functions were being called without a `Policy` argument, thus introducing double- and float-promotion in violation of the user-provided policy. The primitive functions in question include `cbrt`, `cos_pi`, `sin_pi`, `lgamma`, `tgamma`, and `polygamma`.  
  
This commit passes the `Policy` argument down the call chain in several locations. As part of this work, a number of internal functions have had a `Policy` argument added to them. These functions include:  
  
* `airy_ai_zero_detail::initial_guess`  
* `airy_bi_zero_detail::initial_guess`  
* `airy_zero::equation_as_10_4_105`  
* `binomial_ccdf` (in special_functions/beta.hpp)  
* `bessel_zero::asymptotic_bessel_j_large_x_2`  
* `bessel_zero::asymptotic_bessel_y_large_x_2`  
* `bessel_zero::equation_as_9_5_26`  
* `bessel_zero::cyl_neumann_zero_detail::equation_nist_10_21_40_a`  
* `bessel_zero::cyl_neumann_zero_detail::equation_nist_10_21_40_b`  
  
The above functions do not have any user-facing documentation, and so I did not preserve their non-policy function signatures.  
  
I do not know of a good way to verify the absence of float-promotion deep down a call chain, and so I have not added any new tests. The existing tests in the `special_fun` and `distribution_tests` suites continue to pass (on my machine, at any rate).  
  
See #398

---
