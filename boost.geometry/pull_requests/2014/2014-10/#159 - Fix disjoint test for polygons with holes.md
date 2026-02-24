# #159 Fix disjoint test for polygons with holes [Closed]

> Username: zerebubuth  
> Created at: 2014-10-13 03:52:49 UTC  
> Updated at: 2014-10-16 17:17:25 UTC  
> Closed at: 2014-10-16 17:17:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/159  

As recommended by @awulkiew, using `point_on_border` to check whether two polygons which don't have intersecting linework are disjoint. The previous PR didn't cover all the possible cases, as @awulkiew pointed out in the comments for #156, so that case is now included as an additional test.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-10-13 10:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-58874434  

Thanks!  
Before merging I'll give the others a chance to catch up.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-10-13 21:22:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#discussion_r18795868  

It is not necessary to specify the tempate-types in the call to point_on_border

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-10-13 21:25:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-58957211  

The solution is not the same.  
The original solution tests, for geometry2, for each possible ring if it might be contained in geometry 1.  
The new solution just takes one point and checks if it is in geometry1.  
  
If geometry2 has multiple rings, all not touching geometry1, and some are inside geometry1 but other are not, this solution will not work. So please don't merge this solution. And a testcase should be added for this (in overlay I have some samples)

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-10-13 22:47:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-58966122  

In the original solution for_each_range() only calls apply() for exterior ring, interior rings aren't taken into account. All rings of other geometry are handled by within().  
  
I can't find a case when the proposed algorithm should fail, could you prepare some picture of such case?  
  
Correct me if I'm wrong. Basically if there are no intersection poins one should check something like this (only once):  
  
```  
if g1.exterior within g2.exterior  
    // this means "between" holes and exterior, not in any hole  
    if g1.exterior within ALL holes  
        return NOT_DISJOINT  
// g1.exterior is outside g2.exterior, this also means that  
// g2.exterior is within g1.exterior  
else  
    // this means that g2 isn't outside, not in any hole  
    if g2.exterior within ALL holes  
        return NOT_DISJOINT  
return DISJOINT  
```  
  
and we'd be forced to check the above only once for both geometries in the same time. Furthermore the first part of the check is exactly the opposite of the second part (if g1.exterior is outside g2.exterior then this means that g2.exterior is within g1.exterior). So not taking into account 1 unnecessary check for exterior rings we could call 2x the partial check switching geometries order, and this is done. Now what does this check should do? It should check if any point lying on an exterior ring of g1 is within the exterior ring of g2 and all its interior rings. So basically it could call `within()` for the Ring/PointOnRing and other Polygon. This would check all rings of a Polygon at the same time, no need to check the manually 1 by 1. At least if we don't care about this redundant second test for exterior rings. And exactly this is done here. In fact the old algorithm did the same (since `for_each_ring()` doesn't work properly) but it was using `point_on_surface()` which may return a Point placed anywhere on the Polygon's surface so there is no guarantee that we'll be testing any ring this way. Am I missing something?  
  
Though I'm nearly sure that my reasoning is ok because I have similar check in `relate()` which is heavily tested.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2014-10-14 01:49:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-58979917  

Sorry, one aspect of the above reasoning is wrong. If the exterior ring of the first Polygon isn't within the exterior ring of the second Polygon it doesn't mean that the exterior ring of the second Polygon is within the exterior ring of the first Polygon. So two checks for exterior rings are needed, there is no unnecessary ones.  
  
The rest is correct. For intersecting Polygons one's exterior ring must be within the other one (or the other one containing the first one, same situation) and it musn't be contained entirely inside a hole. If those conditions are met, they must be intersecting each other.  
  
The other way around, if Polygon's exterior ring is not within the other Polygon's exterior ring or it's inside one of the holes, the Polygons are disjoint. Otherwise they aren't.  
  
Even if we had g1.exterior_ring outside g2.exterior_ring but g1.hole inside g2.hole then g2.exterior_ring would have to be "inside" (within) g1.exterior_ring and "outside" (within) g1.hole. So the second check would return NOT_DISJOINT.  
  
In case you were confused by my usage of "within". When I'm saying "within hole" I have in mind the actual result of within() function called for a hole. It would return true if something was contained in the interior of a Polygon (not "inside" a hole, so in the exterior).

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-10-15 07:25:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-59167396  

Same comment here as in the already merged PR #158 : the PR should be filed with respect to the develop branch rather than the master branch.  
So procedure-wise, I think we can keep the discussion going here. Once the discussion is over, the PR should be closed and another one should be filed wrt the develop branch. Cherry-picking commits should make this pretty easy to do.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-10-15 10:31:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-59186523  

Sorry, I didn't noticed the master branch :/  
I'll fix it...

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2014-10-15 10:59:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-59189188  

About rings_containing. I had a scenario in mind with multi-polygons, but this is not called for that, so that does not apply here. Considering a point from an exterior ring and checking if it is inside the other whole geometry including holes, and v.v., is OK.   
  
It is also much faster than using point_on_surface.  
  
One small thing left, this solution requires point_on_border to be a point on the exterior border. But that is implemented like that.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2014-10-15 13:09:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-59202094  

OFFTOPIC:  
  
Regarding the `point_on_border`.  
  
Maybe then we should think about changing the name of this function? Or maybe just realize/define that a "border" is not an DE9IM "boundary". E.g. this function will return a Point on a Linestring even if the Linestring is a LinearRing which according to the OGC specs doesn't have a boundary.  
So "border" could be a name for an "exterior boundary" but then for Linestrings it has less sense and still is invalid for those which has no boundary. Though I can't find a good name for it.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2014-10-15 13:52:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-59208081  

I think on_border is basically OK (though on_boundary was maybe better - but this function is released long ago and should probably not be renamed). IMO we just have to document that for a polygon it is somewhere on the exterior ring.  
It is not OGC but named analog to the (then not yet existing) point_on_surface

---

## Comment 11

> Username: awulkiew  
> Created_at: 2014-10-15 15:11:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-59221441  

@zerebubuth Because we're using GitFlow workflow model at Boost.Geometry, the fixes and new features must be merged in develop branch. So please prepare a new PR and change the base branch as it's described here: https://github.com/boostorg/geometry/wiki/Contribution-Tutorial#pull-request

---

## Comment 12

> Username: zerebubuth  
> Created_at: 2014-10-16 17:17:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/159#issuecomment-59397074  

Closed in favour of #162.

---
