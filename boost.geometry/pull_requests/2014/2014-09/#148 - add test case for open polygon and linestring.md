# #148 [test][algorithms][distance] add test case for open polygon and linestring [Merged]

> Username: mkaravel  
> Created at: 2014-09-30 14:16:37 UTC  
> Updated at: 2014-10-01 06:34:59 UTC  
> Merged at: 2014-09-30 15:46:03 UTC  
> Closed at: 2014-09-30 15:46:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/148  

In the added test case the distance is materialized between the linestring and the last (implicit) segment of the polygon.  
  
The test case currently fails, but will succeed once PR #147 is merged.

---
