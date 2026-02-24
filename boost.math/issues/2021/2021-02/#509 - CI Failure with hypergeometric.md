# #509 - CI Failure with hypergeometric [Closed]

> Username: mborland  
> Created at: 2021-02-02 16:21:35 UTC  
> Updated at: 2021-08-17 19:08:22 UTC  
> Closed at: 2021-08-17 19:08:22 UTC  
> Url: https://github.com/boostorg/math/issues/509  

The following warning ([-Woverflow]) is given many times using GCC-9 CXXSTD=17 on PPC64LE arch:  
  
```  
In file included from test_1F1.hpp:79,  
  
                 from test_1F1.cpp:6:  
  
hypergeometric_1F1_big.ipp:102:7: warning: floating constant truncated to zero [-Woverflow]  
  
  102 |       { SC_(-8.1472375000000000000000000000000000000000e+05), SC_(1.3586878906250000000000000000000000000000e+04), SC_(1.5873352050781250000000000000000000000000e+01), SC_(1.9976990618127286269329812204620520231089e-430) },  
  
      |       ^  
  
hypergeometric_1F1_big.ipp:141:7: warning: floating constant truncated to zero [-Woverflow]  
  
  141 |       { SC_(-1.3547703125000000000000000000000000000000e+04), SC_(-1.3586878906250000000000000000000000000000e+04), SC_(-1.0437607421875000000000000000000000000000e+03), SC_(9.1167895022127982467949315979483554606337e-453) },  
```  
  
See [here](https://travis-ci.org/github/mborland/math/jobs/756971704#L908) for more information. If I remember correctly @evanmiller submitted a patch for something very similar. This likely leads to a large number of test failures [here.](https://travis-ci.org/github/mborland/math/jobs/756971704#L1100)

---

## Comment 1

> Username: evanmiller  
> Created at: 2021-02-02 16:39:43 UTC  
> Url: https://github.com/boostorg/math/issues/509#issuecomment-771772318  

The patch that I submitted (and which was merged) was #469.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-05 19:22:53 UTC  
> Url: https://github.com/boostorg/math/issues/509#issuecomment-774238195  

No those test failure are due to the function returning a NaN: it should basically never do that, especially when the result is say 1, as it should be for the first few failures.  This probably needs debugging on that architecture to figure this out :(
