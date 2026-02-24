# #180 Feature/distance [Merged]

> Username: mkaravel  
> Created at: 2014-11-07 21:32:05 UTC  
> Updated at: 2014-11-10 10:51:15 UTC  
> Merged at: 2014-11-10 10:51:15 UTC  
> Closed at: 2014-11-10 10:51:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/180  

This PR refers to distance computations in the spherical equatorial coordinate system.  
  
Changes:  
- Cleanup of unit tests for distance for pointlike/pointlike geometries  
- Addition of testing for pointlike/pointlike geometries using a comparable strategy  
- Addition of unit test for pointlike/linear geometries

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-11-07 21:35:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/180#issuecomment-62217475  

The P/L unit test submitted with this PR needs the bug fix submitted in PR #179.  
It can be merged independently from PR #179, but the P/L unit test will be failing until PR #179 is merged.

---
