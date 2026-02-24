# #467 - Test failure in cohen_acceleration_test with Apple Clang 12.0 and C++17 [Closed]

> Username: evanmiller  
> Created at: 2020-12-23 01:13:10 UTC  
> Updated at: 2021-01-08 13:08:21 UTC  
> Closed at: 2021-01-08 13:08:21 UTC  
> Url: https://github.com/boostorg/math/issues/467  

Full log:  
  
https://github.com/evanmiller/math/runs/1597837166  
  
Relevant portion (starts at line 488 above):  
  
```  
testing.capture-output ../../../bin.v2/libs/math/test/cohen_acceleration_test.test/clang-darwin-12.0/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/cohen_acceleration_test.run  
====== BEGIN OUTPUT ======  
Error at cohen_acceleration_test.cpp:test_divergent:68:  
  ULP distance in long double precision is 134, which exceeds 80, error/ulps  = 1.67.  
  Expected: +0.225791352644727432374 = +0xe.735d92d5098ae24p-6  
  Computed: +0.225791352644727430558 = +0xe.735d92d5098ad9ep-6  
  Mollified relative error: +1.81603863891321992696e-18  
Error count: 1, total ulp distance = 134  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-23 12:01:50 UTC  
> Url: https://github.com/boostorg/math/issues/467#issuecomment-750225911  

@NAThompson

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-12-23 23:30:14 UTC  
> Url: https://github.com/boostorg/math/issues/467#issuecomment-750563549  

There's no problem just increasing the tolerance on this one to 134; 80 ULPs was just empirical anyway-we have no theoretical reason to expect 80 ULP accuracy.

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-01-08 13:08:21 UTC  
> Url: https://github.com/boostorg/math/issues/467#issuecomment-756747031  

Should be fixed in 5fc9214.
