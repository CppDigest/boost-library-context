# #175 Fix/cartesian segments intersection [Merged]

> Username: mkaravel  
> Created at: 2014-11-04 20:11:42 UTC  
> Updated at: 2014-11-05 06:28:40 UTC  
> Merged at: 2014-11-04 21:20:56 UTC  
> Closed at: 2014-11-04 21:20:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/175  

This PR fixes a bug in Cartesian segment-segment intersection strategy.  
  
**Bug:** when exactly one segment degenerates to a point and is collinear with the other segment, the strategy always sets the intersection count to 1.  
  
**Fix:** check if the degenerate segment lies within the non-degenerate one  
before setting the intersection count; if the point is outside the non-degenerate  
segment declare the two segments as disjoint  
  
Some additional test cases have also been added in the distance unit tests. Some of them have triggered the bug mentioned above.  
  
Test cases related to this bug will be added in the disjoint unit tests once PR #129 is merged.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-11-04 20:47:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/175#issuecomment-61711633  

Thanks Menelaos, I'm OK with merging

---
