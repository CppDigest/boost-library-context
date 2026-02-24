# #1038 Correct non-central-t series convergence bug. [Merged]

> Username: jzmaddock  
> Created at: 2023-10-13 17:45:24 UTC  
> Updated at: 2024-01-24 21:33:42 UTC  
> Merged at: 2023-10-14 08:26:12 UTC  
> Closed at: 2023-10-14 08:26:12 UTC  
> Url: https://github.com/boostorg/math/pull/1038  

Fixes https://github.com/boostorg/math/issues/1035. See also https://github.com/scipy/scipy/issues/19348. Accuracy in left tail is still poor, and the reflection formula appears to be to blame as it's use causes the series to cancel out the first term, but it appears we have no real choice in the matter here.  At least we do now get a few digits correct.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-10-14 08:51:17 UTC  
> Url: https://github.com/boostorg/math/pull/1038#issuecomment-1762744546  

Thanks for getting this one John.

---
