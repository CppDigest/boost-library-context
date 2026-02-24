# #375 - BG takes on the globe [Closed]

> Username: rconde01  
> Created at: 2017-01-08 00:26:38 UTC  
> Updated at: 2017-01-10 15:30:34 UTC  
> Closed at: 2017-01-10 14:37:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/375  

I watched/read this talk from meeting c++ 2015...has there been any progress?

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-01-10 14:37:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/375#issuecomment-271591269  

Yes, have a look at:  
 - release notes (after Boost 1.60): http://www.boost.org/libs/geometry/doc/html/geometry/release_notes.html  
 - pull requests (closed since Dec 2015 and open): https://github.com/boostorg/geometry/pulls?q=is%3Aopen+is%3Apr  
  
In general most of the algorithms (relational and set operations, envelope, distance PtLike-Any, area) in spherical equatorial are supported (besides some combinations of geometries in distance, buffer, centroid) and we're working on the same for geographic CS, most of the work is finished. We plan to release it in Boost 1.64.

---

## Comment 2

> Username: rconde01  
> Created at: 2017-01-10 15:30:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/375#issuecomment-271605901  

Great news!
