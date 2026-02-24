# #58 Addition of the barycentric rational interpolant. [Closed]

> Username: NAThompson  
> Created at: 2017-02-28 21:38:26 UTC  
> Updated at: 2017-05-11 15:35:01 UTC  
> Closed at: 2017-05-08 17:44:01 UTC  
> Url: https://github.com/boostorg/math/pull/58  

This commit can be regarded as a "friend" to the cubic b spline, filling out the feature set.  
Whereas the cubic b spline interpolates uniformly spaced data, the barycentric rational interpolant can interpolate arbitrarily spaced data.  
The price that is paid is that evaluation of the interpolant is O(N), rather than O(1) for the cubic b spline interpolant.  
  
The routine supports essentially arbitrary approximation order d; with error of O(h^d+1).  
It is cppcheck-clean, run under AddressSanitizer and UndefinedBehaviorSanitizer, and works with boost::multiprecision.  
  
References:  
  
1) Floater, M.S. & Hormann, K. Numer. Math. (2007) 107: 315. doi:10.1007/s00211-007-0093-y  
  
2) Numerical Recipes, Section 3.4.1

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-05-08 17:44:01 UTC  
> Url: https://github.com/boostorg/math/pull/58#issuecomment-299937769  

This, along with the cubic spline code is now in develop (not sure why it isn't showing up as merged, but there we go).  
  
I've made some minor changes to both to fix some compiler errors, missing #includes etc.  Also extended the examples, and moved the docs around a bit.  Can you take a look at https://www.dropbox.com/s/9uwe1tujc0p12b5/math.pdf?dl=0 and make sure the docs are OK? There are some minor formatting errors, but otherwise look OK to me now.  These two interpolators are in their own sub-section under "tools" BTW.

---

## Comment 2

> Username: pabristow  
> Created_at: 2017-05-11 10:58:16 UTC  
> Url: https://github.com/boostorg/math/pull/58#issuecomment-300755547  

This looks good to me.  Very useful tools. Nice examples too.  Pretty obvious how they work I think (novice novices will use this code!).  But I did notice several typos and trivial not-niceties.  (Most seriously a dud character in a comment). We might even run to an umlaut for Prof Dr Schoedinger ;-) Could provide more links internally, for example to root finding tools, and externally, and additions to the indexes.  I suggest that at some point I go through this with my nitcomb and then commit and push.  But I don't want to collide with other changes, so tell me when would be convenient.

---
