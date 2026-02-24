# #324 Bugfix/exp sinh quardature complex non standard range error [Merged]

> Username: CGJackson  
> Created at: 2020-03-10 19:18:15 UTC  
> Updated at: 2020-03-13 10:09:11 UTC  
> Merged at: 2020-03-13 10:09:10 UTC  
> Closed at: 2020-03-13 10:09:10 UTC  
> Url: https://github.com/boostorg/math/pull/324  

I found a bug where the exp_sinh integrator failed to compile when used to integrate function returning a complex number over a non-native range (that is not from 0 to infinity).  
  
This turned out to be due to the functions which mapped the integral back onto the native range requiring the function being integrated to return the same type that was used for the integration range, rather than the type inferred from the integrand.   
  
I have fixed the bug and added some tests to cover the case of a complex valued function being integrated over a non-native range.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-03-10 21:23:49 UTC  
> Url: https://github.com/boostorg/math/pull/324#issuecomment-597324868  

@CGJackson : Huge thanks for this!

---
