# #329 [algorithms][intersection] Fixes liang_barsky for integral data [Merged]

> Username: springmeyer  
> Created at: 2015-10-06 21:13:14 UTC  
> Updated at: 2015-10-14 22:38:15 UTC  
> Merged at: 2015-10-14 21:41:08 UTC  
> Closed at: 2015-10-14 21:41:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/329  

I've noticed that `bg::intersection` fails to produce correct results when the input geometry and clipping box use integral types for coordinates.  
  
The reduced testcase at https://github.com/springmeyer/boost-geometry-line-clipping-testcase/blob/master/bg-testcase.cpp produces this result with current boost geometry:  
  
```  
LINESTRING(-10 0,10 0)  
LINESTRING(-20 0)  
```  
  
And produces the correct results (they should be the same) with the fix:  
  
```  
LINESTRING(-10 0,10 0)  
LINESTRING(-10 0,10 0)  
```  
  
Per https://github.com/mapbox/mapnik-vector-tile/pull/102 I found and have been using a workaround for this problem: using `linear_ring` as the clipping shape which invokes a different code path entirely.  
  
This is my first time contributing to `boost::geometry` so let me know if you'd like this report somewhere else or any other things I can do to help. I presume that clipping a polygon with a `box` instead of a `linear_ring` will be slightly faster?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-10-08 18:04:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/329#issuecomment-146640168  

Welcome and thanks for the fix!  
  
So the problem is that the intermediate values are truncated to integral type, is that correct?  
  
There is no need to report the issue elsewhere. We greately appreciate the PRs as the review process is very convenient.  
  
Regarding the performance it looks like it should be faster since in the case of Polygon^Linestring the intersections of segments of both geometries would have to be calculated. This operation would have O(NlogN) complexity, where N is the number of points of a Polygon, the number of points of a Linestring would be constant. In the case of Polygon^Box it should be done in O(N).

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-10-08 18:05:59 UTC  
> Updated_at: 2015-10-14 18:36:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/329#discussion_r41546934  

I suggest to change the name of PointType to CoordinateType since neither `p` nor `q` are not Points.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-10-08 18:16:59 UTC  
> Updated_at: 2015-10-14 18:36:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/329#discussion_r41548274  

Type `double` is always used as an intermediate calculation type. This may be a problem in the case of user-defined coordinate types which could have greater precision than `double`, in such case the precision would be lost during casting done above. In places like this we use `select_most_precise<>` utility (see the `geometry/util` directory). Here it could be used like this:  
  
```  
typedef typename select_most_precise<coordinate_type, double>::type calc_type;  
```

---

## Comment 4

> Username: springmeyer  
> Created_at: 2015-10-14 18:38:19 UTC  
> Updated_at: 2015-10-14 22:08:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/329#issuecomment-148150163  

@awulkiew - thank you very much for the code review. I've made both of the suggested changes in https://github.com/springmeyer/geometry/commit/6f214c54c61cac2863c295e4825eea3c4e9332a7 and confirmed that the testcase still returns the correct (fixed) results. Let me know if anything else needs to happen to land this?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-10-14 20:29:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/329#issuecomment-148186216  

It's fine, thanks! Are everyone ok with merging?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-10-14 21:09:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/329#issuecomment-148201249  

Thanks for the fix! I'm OK with merging.

---
