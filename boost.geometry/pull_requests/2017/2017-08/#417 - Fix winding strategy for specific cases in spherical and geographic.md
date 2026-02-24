# #417 Fix winding strategy for specific cases in spherical and geographic. [Merged]

> Username: awulkiew  
> Created at: 2017-08-21 23:15:40 UTC  
> Updated at: 2017-08-30 12:47:58 UTC  
> Merged at: 2017-08-30 11:43:03 UTC  
> Closed at: 2017-08-30 11:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/417  

This PR fixes sinding strategy by adding separate strategies per CS: `cartesian_winding`, `spherical_winding` and `geographic_winding`. The last one taking geodesic `FormulaPolicy` and `Spheroid` (interface consistent with other geographic strategies).  
  
The agnostic agnostic winding strategy is left for backward compatibility (with Boost 1.63 and below because SideStrategy parameter added in 1.64 was removed). This agnostic version internally is using one of the CS-specific strategies mentioned above based on `cs_tag<>` of `Point` parameter.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2017-08-23 07:54:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/417#issuecomment-324251070  

Looks good to me! Thanks.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2017-08-23 07:54:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/417#issuecomment-324251146  

Sorry, wrong button, reopened

---

## Comment 3

> Username: vissarion  
> Created_at: 2017-08-29 10:36:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/417#issuecomment-325624770  

I am ok with it too. Thanks

---
