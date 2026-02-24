# #692 Constexpr logb and ilogb [Merged]

> Username: mborland  
> Created at: 2021-09-17 18:08:13 UTC  
> Updated at: 2021-09-18 12:53:21 UTC  
> Merged at: 2021-09-18 08:47:37 UTC  
> Closed at: 2021-09-18 08:47:37 UTC  
> Url: https://github.com/boostorg/math/pull/692  

Implements: `logb`, `logbf`, `logbl`, `ilogb`, `ilogbf`, and `ilogbl`

---

## Comment 1

> Username: mborland  
> Created_at: 2021-09-18 06:37:25 UTC  
> Updated_at: 2021-09-18 06:39:04 UTC  
> Url: https://github.com/boostorg/math/pull/692#issuecomment-922222522  

@jzmaddock and @NAThompson pending review this should be good to go. The one CI failure was `USAN` took too long on the autodiff tests so the build was killed.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-09-18 08:47:30 UTC  
> Url: https://github.com/boostorg/math/pull/692#issuecomment-922242780  

All looks good to me, merging.

---
