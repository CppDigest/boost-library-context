# #337 Fix/centroid multipolygon empty exterior ring [Closed]

> Username: mkaravel  
> Created at: 2015-11-28 07:20:22 UTC  
> Updated at: 2021-09-22 06:56:14 UTC  
> Closed at: 2021-09-22 06:56:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/337  

This PR is a follow up on PR #336. It PR addresses the behavior of the `centroid` algorithms for certain kinds of multi-polygons.  
  
The multi-polygons of interest are (invalid) multi-polygons that contains one of more polygons whose exterior ring is empty and at the same time have one or more non-empty interior rings. For such multi-polygons an exception should be thrown. See also related discussion in PR #336. This PR fixes the behavior of the `centroid` algorithm by triggering an exception when multi-polygons (like the ones described above) are given as input to `centroid`.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-11-28 19:32:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/337#discussion_r46083509  

`centroid_polygon_state` is used by `centroid_polygon` and `centroid_multi`. In the first case the emptiness or rather the correctness of a range is checked already by `range_ok()`, so with this addition the same check is done two times. There is only a problem in the case of centroid_multi. Therefore this additional emptiness check should be done in centroid_multi for each element of multi geometry but only if the single geometry was a polygon. So there could be a utility checking if single geometry is ok (like `range_ok`) dispatched by single-geometry tag, with empty implementation for non-Polygons.  
  
An alternative would be to remove the emptiness checks (`range_ok`) from `centroid_polygon` and move it to `centroid_polygon_state`.

---
