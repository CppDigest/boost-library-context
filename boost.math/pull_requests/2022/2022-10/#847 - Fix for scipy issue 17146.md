# #847 Fix for scipy issue 17146 [Merged]

> Username: mborland  
> Created at: 2022-10-16 17:43:23 UTC  
> Updated at: 2022-10-24 08:19:09 UTC  
> Merged at: 2022-10-18 00:54:46 UTC  
> Closed at: 2022-10-18 00:54:46 UTC  
> Url: https://github.com/boostorg/math/pull/847  

xref: https://github.com/scipy/scipy/issues/17146

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-24 08:19:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/847#pullrequestreview-1152722105  

📁 include/boost/math/distributions/binomial.hpp

```diff
 487 |-         { // binomial coeffic (n 0) = 1,
 488 |-           // n ^ 0 = 1
 489 |-           return pow(1 - dist.success_fraction(), n);
```

> Username: jzmaddock  
> Created_at: 2022-10-24 08:19:09 UTC  
> Url: https://github.com/boostorg/math/pull/847#discussion_r1003016567  

Thanks for taking this on Matt, and with apologies for being AWOL: I'm just catching up with everything while writing the release notes for 1.81.  
  
Since ibeta_derivative is relatively heavyweight, and the alternative formula is so simple, I think it would be nice to have retained the alternative form here.  The trick would be to take logs and use `exp(n*log1p(-dist.success_fraction()))` in the case where `- dist.success_fraction()` is small.  Maybe try 0.125 as a cutoff?  Might need some experimenting to see where the errors start to rise.  
  
This can certainly wait till after the next release though...


---
