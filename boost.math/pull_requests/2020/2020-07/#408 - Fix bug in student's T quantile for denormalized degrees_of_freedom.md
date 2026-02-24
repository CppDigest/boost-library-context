# #408 Fix bug in student's T quantile for denormalized degrees_of_freedom. [Merged]

> Username: jzmaddock  
> Created at: 2020-07-24 19:13:01 UTC  
> Updated at: 2020-07-27 18:36:16 UTC  
> Merged at: 2020-07-27 18:34:54 UTC  
> Closed at: 2020-07-27 18:34:54 UTC  
> Url: https://github.com/boostorg/math/pull/408  

See https://github.com/boostorg/math/issues/406

---

## Review 1 [Commented]

> Username: evanmiller  
> Created_at: 2020-07-25 00:55:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/408#pullrequestreview-455246567  

📁 include/boost/math/special_functions/detail/ibeta_inverse.hpp

```diff
 692 |+       if (overflow || !(boost::math::isfinite)(bet))
 693 |+       {
 694 |+          xg = exp((boost::math::lgamma(a + 1, pol) + boost::math::lgamma(b + 1) - boost::math::lgamma(a + b + 1) + log(p) - log(b) + log(a + b)) / a);
```

> Username: evanmiller  
> Created_at: 2020-07-25 00:55:49 UTC  
> Updated_at: 2020-07-25 17:30:19 UTC  
> Url: https://github.com/boostorg/math/pull/408#discussion_r460343625  

Shouldn't the 2nd and 3rd calls to `lgamma` here include the policy as the 2nd argument?


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-07-25 07:50:27 UTC  
> Url: https://github.com/boostorg/math/pull/408#issuecomment-663824668  

>Shouldn't the 2nd and 3rd calls to lgamma here include the policy as the 2nd argument?  
  
Doh!  Yes.

---

## Review 3 [Commented]

> Username: evanmiller  
> Created_at: 2020-07-25 14:20:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/408#pullrequestreview-455287928  

📁 include/boost/math/special_functions/detail/ibeta_inverse.hpp

```diff
 698 |+       if (overflow || !(boost::math::isfinite)(bet))
 699 |+       {
 700 |+          xg = exp((boost::math::lgamma(a + 1, pol) + boost::math::lgamma(b + 1, pol) - boost::math::lgamma(a + b + 1, pol) + log(p) - log(b) + log(a + b)) / a);
```

> Username: evanmiller  
> Created_at: 2020-07-25 14:20:47 UTC  
> Updated_at: 2020-07-25 17:30:19 UTC  
> Url: https://github.com/boostorg/math/pull/408#discussion_r460409448  

Maybe I'm missing something, but why not reduce  
  
`lgamma(b+1) - log(b) - lgamma(a+b+1) + log(a+b)`  
  
To just `lgamma(b) - lgamma(a+b)`  
  
?

> Username: jzmaddock  
> Created_at: 2020-07-25 16:21:00 UTC  
> Updated_at: 2020-07-25 17:30:19 UTC  
> Url: https://github.com/boostorg/math/pull/408#discussion_r460420046  

The branch deals with a,b < 1 and the problem occurs when one of a,b is so small that 1/a (for example) overflows, so I had issues with lgamma overflowing as well.... though in point of fact I guess that may be a bug as well :(

> Username: jzmaddock  
> Created_at: 2020-07-25 17:31:03 UTC  
> Url: https://github.com/boostorg/math/pull/408#discussion_r460426485  

It was a bug in lgamma: there was actually a fix for this already in place, it just didn't quite go far enough.


---
