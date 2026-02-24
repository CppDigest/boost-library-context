# #339 Fix andoyer [Merged]

> Username: awulkiew  
> Created at: 2016-01-18 23:51:14 UTC  
> Updated at: 2017-03-20 14:29:21 UTC  
> Merged at: 2016-01-25 18:39:57 UTC  
> Closed at: 2016-01-25 18:39:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/339  

This PR is related to #338  
  
It fixes `andoyer_inverse` formula for points in antipodal areas. This formula can be used to calculate distances and azimuths between points using first order Andoyer-Lambert-Forsyth type approximation. Besides fixed distances the azimuths for points in the vincinity of antipodal point are convergent to the azimuth calculated for antipodal point.  
  
This formula may be used in the Andoyer strategy which currently uses separate, older implementation.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2016-01-25 18:39:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/339#issuecomment-174615371  

`andoyer_inverse` formula is now used in the implementation of `andoyer` distance strategy instead of the original implementation.

---
