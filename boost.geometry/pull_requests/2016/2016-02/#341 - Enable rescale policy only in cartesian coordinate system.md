# #341 Enable rescale policy only in cartesian coordinate system. [Merged]

> Username: awulkiew  
> Created at: 2016-02-04 15:40:00 UTC  
> Updated at: 2017-03-20 14:30:24 UTC  
> Merged at: 2016-03-06 21:41:39 UTC  
> Closed at: 2016-03-06 21:41:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/341  

This PR fixes the regression reported here:  
https://svn.boost.org/trac/boost/ticket/11789  
  
An assertion fails in `expand()` called from `sectionalize()` for non-cartesian geometries. It's because it is called for Points containing very big integral coordinates. The reason is that currently, by default, floating-point coordinates are rescaled into integral coordinates internally in set operations and these integral coordinates are passed into expand() to calculate bounding boxes. This also means that since these coordinates are normalized, the result is not correct.   
  
After merging this PR rescaling will be enabled only for geometries in cartesian CS.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2016-03-03 11:53:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/341#issuecomment-191724320  

@barendgehrels are you ok with this change?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2016-03-06 17:22:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/341#issuecomment-192938631  

I am OK with merging this, thanks for the change

---
