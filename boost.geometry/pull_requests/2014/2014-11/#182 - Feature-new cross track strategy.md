# #182 Feature/new cross track strategy [Merged]

> Username: mkaravel  
> Created at: 2014-11-19 22:33:51 UTC  
> Updated at: 2015-02-03 20:03:26 UTC  
> Merged at: 2014-12-09 22:49:42 UTC  
> Closed at: 2014-12-09 22:49:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/182  

In this PR the `cross_track<>` distance strategy is implemented in a different way:  
- It has a comparable counterpart, which among other things enables the comparisons with distances computed with `comparable::haversine<>`.  
- It is more efficient with respect to the current `cross_track<>` strategy implementation.  
  
I have performed the following benchmark: I have considered the segment `SEGMENT(0.05 0, 179.95 0)` which is almost half the equator (units in degrees). Then I took `N` random points on the sphere, where `N` took the values 10^2, 10^3, 10^4, 10^5, 10^6 and 10^7. Using these point sets I computed:  
1. The distance of each point to the segment.  
2. The distance of the points seen as a multipoint to the segment, using `bg::distance`. Notice than in this case the distance algorithm computes the closest point/segment pair using comparable distances, and then returns this distance as is if the strategy passed is a comparable one, or computes the true distance for the closest point/segment pair if a non-comparable strategy is passed.  
  
In each case I used the old cross track strategy, the new one, and the new comparable one (the last one to measure the time difference when computing comparable distances). For each case I measured the relative gain as a percentage:  
  
```  
   gain = 100 * (TIME(old_strategy) - TIME(new_strategy)) / TIME(old_strategy)  
```  
- For the old/new non-comparable strategies and for Case (1) the gain was between 22 and 30%.  
- For the old/new comparable strategies and for Case (1) the gain was between 21 and 44%.  
- For the old/new non-comparable strategies and for Case (2) the gain was between 29 and 32%.  
- For the old/new comparable strategies and for Case (2) the gain was between 28 and 52%.  
  
The reason for choosing this particular segment is that a random point on the sphere has about the same probability (equal approximately to 1/2) to be closer to one of the endpoints of the segment or the segment's interior, thus covering equally both major cases in the `cross_track<>` strategy computation algorithm.  
  
Finally, the unit test for the computation of distances between pointlike and linear geometries has been updated to account for the new strategy proposed.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-12-05 11:52:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/182#issuecomment-65780347  

The last commit offers a more numerically robust way for computing the cross track distance when the point projects inside the spherical segment.  
At a more technical level, the original implementation was computing a expression of the form (`x` ranges from 0 to 0.25):  
  
```  
    0.5 - sqrt(0.25 - x)  
```  
  
This expression produces large numerical errors when `x` is close to zero. The evaluation if this expression is substituted by the expression (at the cost of an additional division) by the expression:  
  
```  
   x / (0.5 + sqrt(0.25 - x))  
```  
  
which is numerically robust.

---
