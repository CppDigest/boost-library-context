# #640 nonfinite_num_get facet fix: honor "legacy" flag to enable "nanq"+"nans" [Merged]

> Username: jblumsch  
> Created at: 2021-06-05 02:28:12 UTC  
> Updated at: 2021-07-05 21:20:58 UTC  
> Merged at: 2021-07-05 21:20:58 UTC  
> Closed at: 2021-07-05 21:20:58 UTC  
> Url: https://github.com/boostorg/math/pull/640  

A hopefully obvious one-line fix to "Facets for Floating-Point Infinities and NaNs".  
  
The documentation states that the "legacy" flag enables support for "nanq" and "nans", but any other flag is not supposed to do so.  
  
The "legacy" flag got lost in 16c57be3 "Fixes for compile tests", but the affected code was broken from the very beginning.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-07-02 16:56:36 UTC  
> Url: https://github.com/boostorg/math/pull/640#issuecomment-873133677  

Did this slip through the cracks? I have really no understanding of what this code does.  
  
@jzmaddock : Could you take a look as well?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-07-02 18:23:41 UTC  
> Url: https://github.com/boostorg/math/pull/640#issuecomment-873179797  

Looks right to me, we should merge once CI completes.  
  
Thanks for the heads up!

---
