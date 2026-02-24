# #462 - Error at linear_regression_test.cpp:test_scaling_relations with Clang++-10 and C++17 [Closed]

> Username: evanmiller  
> Created at: 2020-12-22 16:12:34 UTC  
> Updated at: 2021-01-12 21:32:29 UTC  
> Closed at: 2021-01-12 21:32:29 UTC  
> Url: https://github.com/boostorg/math/issues/462  

I've been experimenting with building Boost math with new-ish compiler versions. I get the following error running when running the "misc" test suite with Clang 10.0.0 and __cplusplus = 201703L:  
  
```  
**passed** ../../../bin.v2/libs/math/test/test_t_test.test/clang-linux-10.0.0/debug/link-static/threading-multi/visibility-hidden/test_t_test.test  
clang-linux.link ../../../bin.v2/libs/math/test/linear_regression_test.test/clang-linux-10.0.0/debug/link-static/threading-multi/visibility-hidden/linear_regression_test  
testing.capture-output ../../../bin.v2/libs/math/test/linear_regression_test.test/clang-linux-10.0.0/debug/link-static/threading-multi/visibility-hidden/linear_regression_test.run  
====== BEGIN OUTPUT ======  
Error at linear_regression_test.cpp:test_scaling_relations:151:  
  ULP distance in float precision is 56, which exceeds 50, error/ulps  = 1.12.  
  Expected: +3.205449104 = +0x1.9a4c28p+1  
  Computed: +3.205462456 = +0x1.9a4c98p+1  
  Mollified relative error: +4.16523244e-06  
Error at linear_regression_test.cpp:test_scaling_relations:151:  
  ULP distance in long double precision is 60, which exceeds 50, error/ulps  = 1.2.  
  Expected: +3.205454929075602930715 = +0xc.d262c6e4ba1342cp-2  
  Computed: +3.205454929075602943725 = +0xc.d262c6e4ba13468p-2  
  Mollified relative error: +4.0588391843581566924e-18  
Error count: 2, total ulp distance = 116  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
You can see the full output here (error message starts around line 2190):  
  
https://github.com/evanmiller/math/runs/1595605908  
  
The host system is Ubuntu 18.04, and the code base is an up-to-date fork of `develop`.

---

## Comment 1

> Username: evanmiller  
> Created at: 2020-12-22 17:24:27 UTC  
> Url: https://github.com/boostorg/math/issues/462#issuecomment-749672790  

Running more tests, I see the same error with G++-10. It also appears with both compilers under C++20.

---

## Comment 2

> Username: mborland  
> Created at: 2020-12-24 07:56:42 UTC  
> Url: https://github.com/boostorg/math/issues/462#issuecomment-750795238  

Confirmed on Fedora 33 with clang and GCC. Replacing `auto` with explicit types seems to do the trick. PR to follow.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-01-11 17:40:35 UTC  
> Url: https://github.com/boostorg/math/issues/462#issuecomment-758110661  

This is showing up in the newly merged github-actions CI: https://github.com/boostorg/math/runs/1681924587?check_suite_focus=true

---

## Comment 4

> Username: NAThompson  
> Created at: 2021-01-11 18:24:49 UTC  
> Url: https://github.com/boostorg/math/issues/462#issuecomment-758136560  

@jzmaddock : Should be fixed in  3b9f5ad.
