# #319 Take special care of NaN coordinates in relate for MultiLinestring [Merged]

> Username: awulkiew  
> Created at: 2015-08-14 16:05:02 UTC  
> Updated at: 2015-08-25 15:00:44 UTC  
> Merged at: 2015-08-20 12:05:15 UTC  
> Closed at: 2015-08-20 12:05:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/319  

In places where a check must be performed, if a Point is one of the endpoints of a Linestring contained in a MultiLinestring, std::sort() and std::equal_range() algorithms are used. With MSVC the assertion in std::equal_range() fails if the elements cannot be reliably compared, i.e. in the case when Points has NaN coordinates.  
  
This PR adds has_nan_coordinate() and used it in relate() implementation.  
  
Geometries containing NaN coordinates can be easily generated with a set operation containing some unreasonable data like very big segments.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-08-20 10:53:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/319#issuecomment-132972380  

I am in favor of merging this PR.  
Thanks a lot Adam.

---
