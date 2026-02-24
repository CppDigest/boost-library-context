# #343 Fix winding strategy for spherical and geographic system. [Merged]

> Username: awulkiew  
> Created at: 2016-03-03 14:52:41 UTC  
> Updated at: 2016-06-21 15:59:05 UTC  
> Merged at: 2016-03-06 21:45:47 UTC  
> Closed at: 2016-03-06 21:45:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/343  

In spherical and geographic CS if a point has the same latitude as both points of a segment it doesn't mean that it lies on the segment. This PR fixes this case by scanning dimension 0 (X) instead of dimension 1 (Y). In spherical and geographic it's guaranteed that if longitude is the same a point lies on a segment.  
  
Furthermore special checks are added for points lying on poles and segments going through poles.  
  
IMPORTANT: The change of dimensions mentioned above influences buffer implementation which uses winding strategy directly and assume that this strategy scans in a specific dimension.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2016-03-06 17:31:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/343#issuecomment-192939443  

Thanks Adam! Also for adapting the buffer.  
I'm OK with merging this.

---
