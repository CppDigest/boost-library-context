# #374 - Comments on the new AGM code [Closed]

> Username: evanmiller  
> Created at: 2020-06-20 18:15:13 UTC  
> Updated at: 2021-09-20 17:19:49 UTC  
> Closed at: 2021-09-20 17:19:49 UTC  
> Url: https://github.com/boostorg/math/issues/374  

I notice there is now AGM (arithmetic-geometric) code in a couple of different places:  
  
https://github.com/boostorg/math/blob/develop/include/boost/math/tools/agm.hpp (General AGM function committed this month)  
  
https://github.com/boostorg/math/blob/develop/include/boost/math/constants/calculate_constants.hpp (Specialized copy of above to compute Gauss's constant)  
  
But there is already AGM code implemented in the Carlson RF integral:  
  
https://github.com/boostorg/math/blob/3cc1ebef5e5ece525770692907e7d10df84ec5a4/include/boost/math/special_functions/ellint_rf.hpp#L93-L107  
  
(I was working with this last one recently in relation to the Jacobi functions.)  
  
I tried using the new AGM code in the RF function, but the test appeared to hang (maybe just extremely slow convergence?).  
  
Good practice would dictate eliminating duplicate code. However, there are significant implementation differences in these two AGM implementations. The stopping criteria are noticeably divergent - The new code tests against `epsilon/512`, whereas the RF code uses `2.7*root_epsilon/fabs(x)`.  
  
The root epsilon is sensible in the RF code, because X and Y are square-rooted before evaluation. However, I think the choice to scale by `fabs(x)` is a significant one, and possibly should be considered in the new AGM code. The new AGM function appears to have been developed with order-1 arguments in mind, but may not work with much larger (or smaller) arguments.  
  
If it's not over-engineering things, perhaps the new AGM function could 1) scale epsilon by |X| and 2) accept a policy argument, so that the RF code and the constant-computation code could set their own epsilon (i.e. to root-epsilon and epsilon/512, respectively).

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-06-20 22:10:23 UTC  
> Updated at: 2020-06-20 22:12:35 UTC  
> Url: https://github.com/boostorg/math/issues/374#issuecomment-647050847  

@evanmiller : Note the test is in the new code is against sqrt(eps) and then rescaled by `g` on every iteration:  
  
```cpp  
 const Real scale = sqrt(std::numeric_limits<Real>::epsilon())/512;  
 while (a-g > scale*g) {  
 ///  
}  
```  
  
The final average gets the error down to ~1ULP.  
  
The reason why your test probably hung is that the new code assumes `a > g`, since it swaps the arguments if `g > a`.

---

## Comment 2

> Username: evanmiller  
> Created at: 2020-06-20 23:09:10 UTC  
> Url: https://github.com/boostorg/math/issues/374#issuecomment-647055368  

You are right - I completely misread the new code! All that is left here is a vague desire to reduce code duplication - and I guess some explanation on the 2.7 factor in the RF code versus the /512 in `tools::agm`. By way of context: I was poking around this part of the code while hunting down some precision issues, and it would be great to know that the RF's AGM is 1 ULP.  
  
The testing hang appears to be in the `<real_concept>` tests (i.e. when I explicitly call `tools::agm` from `ellint_rf_imp`). The culprit is `std::numeric_limits<Real>::epsilon()`, which returns 0 for `real_concept` types. It appears that `tools::root_epsilon<Real>()` seems to be more robust - I can open a small pull request for this if you'd like.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-06-21 00:26:31 UTC  
> Url: https://github.com/boostorg/math/issues/374#issuecomment-647061382  

> All that is left here is a vague desire to reduce code duplication - and I guess some explanation on the 2.7 factor in the RF code versus the /512 in tools::agm.  
  
Yeah, it's  a good question. I did random sampling in the AGM to produce an ulps plot at with and without the division by 512; IIRC there were a few just samples just below two ulps when I didn't divide by 512, and when dividing by 512 it was at worst something like 1.3 ulps? The ulps plot code for agm is in `reporting/accuracy/test_agm.cpp`. I imagine the other agm implementation "knows" something about it's inputs, letting the tolerance be a bit looser, but to be honest I don't know.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-06-21 08:19:36 UTC  
> Url: https://github.com/boostorg/math/issues/374#issuecomment-647096125  

FYI the `*2.7` is taken straight from "NUMERICAL COMPUTATION OF REAL OR COMPLEX  
ELLIPTIC INTEGRALS", B. C. Carlson, eq.44.
