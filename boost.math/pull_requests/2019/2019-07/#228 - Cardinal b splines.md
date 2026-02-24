# #228 Cardinal b splines [Merged]

> Username: NAThompson  
> Created at: 2019-07-15 14:43:46 UTC  
> Updated at: 2019-07-22 11:43:38 UTC  
> Merged at: 2019-07-22 11:43:33 UTC  
> Closed at: 2019-07-22 11:43:33 UTC  
> Url: https://github.com/boostorg/math/pull/228  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-07-16 09:59:31 UTC  
> Url: https://github.com/boostorg/math/pull/228#issuecomment-511751225  

That looks fine to me, two quick questions:  
  
1) Do we have changes in develop unmerged to master (ie waiting for the closed-for-beta period to end)?  
2) Should the basis be provided as separate arguments, ie as:  
  
```  
template<unsigned n, typename Real>  
   Real cardinal_b_spline(Real x, Real basis_start = -1, Real basis_end = 1);  
```  
  
Rather than providing a separate function for the [0,1] case?  
  
I don't have a strong opinion on this, just asking ;)

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-07-16 11:39:58 UTC  
> Url: https://github.com/boostorg/math/pull/228#issuecomment-511780426  

> Do we have changes in develop unmerged to master (ie waiting for the closed-for-beta period to end)?  
  
No, develop got merged to master last week and I'm aware of no problems with it. If there's some bugs discovered post-beta we'll have to cherry pick.  
  
>     Should the basis be provided as separate arguments, ie as:  
  
template<unsigned n, typename Real>  
   Real cardinal_b_spline(Real x, Real basis_start = -1, Real basis_end = 1);  
  
Rather than providing a separate function for the [0,1] case?  
  
This is actually closer in spirit to what the Cox paper does, namely he creates B-splines on arbitrary knot sets and so the basis functions can have arbitrary support. That loses some symmetry relative to what I'm doing here, but it's something I would like to deploy in the future. But as for now, I would say no.

---

## Comment 3

> Username: pabristow  
> Created_at: 2019-07-16 15:55:45 UTC  
> Updated_at: 2019-07-17 08:15:42 UTC  
> Url: https://github.com/boostorg/math/pull/228#issuecomment-511877400  

Ignore - wrong conversation :-(

---
