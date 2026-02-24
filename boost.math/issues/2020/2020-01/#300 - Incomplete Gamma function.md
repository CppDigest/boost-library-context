# #300 - Incomplete Gamma function [Closed]

> Username: vegaonline  
> Created at: 2020-01-11 15:34:56 UTC  
> Updated at: 2020-01-11 16:34:34 UTC  
> Closed at: 2020-01-11 16:34:34 UTC  
> Url: https://github.com/boostorg/math/issues/300  

I need to compute incomplete gamma function lower for a complex number say (0.0+1.0*I).  As I tried, I found error. Is there any way to compute tgamma_lower(alpha, z)?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-11 16:34:34 UTC  
> Url: https://github.com/boostorg/math/issues/300#issuecomment-573332019  

We don't currently support that for complex arguments - only the real valued case.
