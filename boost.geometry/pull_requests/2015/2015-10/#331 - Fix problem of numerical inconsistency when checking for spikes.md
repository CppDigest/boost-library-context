# #331 Fix problem of numerical inconsistency when checking for spikes [Merged]

> Username: mkaravel  
> Created at: 2015-10-12 09:16:10 UTC  
> Updated at: 2015-10-14 08:14:27 UTC  
> Merged at: 2015-10-14 06:24:29 UTC  
> Closed at: 2015-10-14 06:24:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/331  

When checking whether a point `q` creates a spike or not with respect to two (ordered) points `a` and `b`, the first check performed is whether `q`, `a` and `b` are collinear. If so, then it is determined whether the vectors `q-b` and `b-a` have the same direction or not. For points with floating-point coordinates, due to rounding errors as well as due to the fact that equality to zero is checked using some tolerance, the three points may be detected as collinear. However, when the directions of the two vectors are checked, computations are done without taking into account any tolerance and this can lead to inconsistent results.  
  
Here is one example geometry triggering this erroneous behavior: consider the polygon with WKT: `POLYGON((-200 0,-207.99999999999997 135,-208 1,-208 0,-208 -276,-200 0))`. When the point `q: (-207.99999999999997 135)` is checked against the points `a: (-208 0)` and `b: (-208 1)`, the three point are found to be collinear. As such, the algorithm checking spikes tries to determine the signs of the coordinates of the differences `q-b` and `b-a`. The difference of the x-coordinate of `q-b` is very close to zero (but not identically equal to it) and its sign triggers the wrong result; instead the difference of the x-coordinates of `q-b` should have been zero and this can be addressed by using `math::equals()` instead of plain comparison operators.  
  
**_Fix:**_ when checking the directions of the vectors `q-b` and `b-a`, compute signs of differences using numerical tolerances, that is using `math::equals()` instead of plain comparison operators.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-10-12 20:14:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/331#issuecomment-147507947  

Thanks, I think I'm OK with merging. If math::sign is not used within the library anymore (this was probably the only case) then I'm OK to remove that function.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-10-13 07:27:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/331#issuecomment-147628505  

`math:sign()` is still used in two strategies: (1) the Cartesian segment-segment intersection strategy, and (2) the Cartesian side of intersection strategy.  
As such, I will not remove it.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-10-13 07:27:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/331#issuecomment-147628564  

@awulkiew Are you okay with merging?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-10-13 17:10:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/331#issuecomment-147780536  

@mkaravel yes, I am, thanks!

---
