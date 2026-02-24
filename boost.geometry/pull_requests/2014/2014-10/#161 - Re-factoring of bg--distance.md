# #161 Re-factoring of bg::distance [Merged]

> Username: mkaravel  
> Created at: 2014-10-14 08:48:06 UTC  
> Updated at: 2014-10-19 20:13:47 UTC  
> Merged at: 2014-10-19 20:13:47 UTC  
> Closed at: 2014-10-19 20:13:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/161  

In this PR I do a major re-factoring of the `boost::geometry::distance` code. The motivation for doing this re-factoring is due to the following reasons:  
- A lot of code can be unified due to the introduction of the segment iterator  
- The code is re-designed to first compute the closest features between two geometries and then compute the distance as the distance of the closest feature. A consequence of this is that there is already code that can serve for future implementations of DB GIS functionalities like `ST_ClosestPair` and `ST_ShortestLine`. The other reason is that changes in boost 1.56 broke functionality related to distances in spherical/geographical coordinate system; with this re-factoring this functionality should be available again.  
  
As part of this re-factoring, a few optimizations where made:  
- On the code side, the dispatching mechanism is now more compact (because of the use of the segment iterator it is possible to write general algorithms applicable to multiple geometries at the same time)  
- On the efficiency side, the R-Tree is now used with segments as objects (for linear and areal geometries), instead of points. For linear geometries this completely avoids the previously existing call to `bg::intersects`. Also there is only one R-Tree now instead of two in the previous version.  
- Also on the efficiency side, the point-to-ring algorithm was optimized: previously when calling the relevant static apply method, both (1) the containment of the point in the geometry and (2) the distance of the point to the boundary were computed. In the new version, the containment of the point is computed first, and if needed, its distance to the boundary. So the new version should be faster for points inside the ring.  
- The point-to-polygon distance computation is also optimized. In the previous version both the containment of the point in the polygon and the distance to its boundary were always computed (with the latter meaning that the distance of the point to the boundary of all rings of the polygon was computed). In the new version, the point is tested for containment in the exterior ring. If it is not contained inside, the distance of the point to the exterior ring is computed. For points inside, the point is tested for containment in each of the inner rings (holes). If such a hole is found, the distance to this hole's boundary is returned, without further consideration of other holes. If no such hole is found, the point is inside the polygon and 0 distance is returned.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-10-14 11:16:20 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18821539  

While I understand the rationale behind the "beyond" name and that "last" may be misleading for some people but AFAIU the "standard" names for a pair of iterators of a Range is [first, last). Those names are used in reference pages, docs, other libraries, STL implementations, etc. I didn't check it but maybe even in the standard itself. See e.g.:  
- http://www.cplusplus.com/reference/algorithm/copy/  
- http://en.cppreference.com/w/cpp/algorithm/copy  
- http://msdn.microsoft.com/en-us/library/x9f6s1wf.aspx  
- https://stdcxx.apache.org/doc/stdlibug/2-2.html  
- http://www.boost.org/doc/libs/1_56_0/libs/algorithm/doc/html/algorithm/CXX11.html  
- http://www.boost.org/doc/libs/1_56_0/libs/geometry/doc/html/geometry/reference/spatial_indexes/boost__geometry__index__rtree/rtree_iterator__iterator_.html ;)  
- https://github.com/boostorg/move/blob/master/include/boost/move/algorithm.hpp  
  
Please consider using "last" instead of "beyond", for a person familiar with the STD naming convention it is more straightforward.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-10-14 11:29:26 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18822011  

Does it mean that to this function an empty range will never be passed? Would it make sense to set it_min to first or beyond/last for an empty range and return?

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-10-14 11:31:02 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18822087  

May the input range be empty? If yes, this would go out of bounds.  
  
Would it make sense to return an empty range if empty input range was passed?

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-10-14 12:48:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#issuecomment-59037017  

@awulkiew `beyond` has been renamed to `last`.  
Regarding empty iterator ranges: these methods are supposed to be called for non-empty ranges.  
So I have kept the assertion in the first one, and added an assertion in the second.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2014-10-14 14:05:29 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18830009  

You changed the "beyond" name to "last" in other place so I guess you agree with me. So I'll point out other occurances like the ones in this class.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-10-14 14:11:56 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18830495  

I personally do not have the slightest problem understanding `beyond`. But this is just me.  
  
To be honest, I prefer `beyond` to `last`, as it is less confusing for me; `last` in my mind can/may refer to the last valid object: `--beyond`.  
  
For the shake of readability and conformity, I changed `beyond` to `last`.  
And to make you happy :-)  
Fine with me to make these changes. I will update the ones above and more that I may find.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-10-14 14:12:13 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18830509  

I'm wondering, what do you think about using the bg::distance() in such places for automatic type deduction? Like:  
  
```  
distance(cf12.first, *cf12.second, strategy);  
```  
  
It should work with both comparable and regular strategies right? AFAIU it would be possible with the "new" interface/implementation structure. Also in other places, like the one below. This way the code woudl be shorter and more readable. Or do you prefer to call the dispatched version explicitly? For clearance?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2014-10-14 14:24:26 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18831357  

Yes I understand the reason, I guess you could say the same about the "end".  
Thanks :) It's just that this naming convention is used everywhere.  
Well, in Boost.Range I saw also [begin, end) and [left, right) in some places.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2014-10-14 14:28:58 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18831658  

Shouldn't here intersects() or covered_by() be used?

---

## Comment 10

> Username: awulkiew  
> Created_at: 2014-10-14 14:31:02 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18831814  

Shouldn't here covered_by() be used?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2014-10-14 14:34:29 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18832053  

I'm not sure if I understand it correctly but I think that if this within() returns true, the Point is in the interior of a Polygon, not inside a hole because holes has the opposite orientation to the exterior ring. So AFAIU it should be `!covered_by(point, *it)`. Can you confirm it?

---

## Comment 12

> Username: mkaravel  
> Created_at: 2014-10-15 05:59:56 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18875846  

Yes, it should work with both comparable and regular strategies.  
  
I prefer the dispatched version. The reason is that I am slightly afraid that using the free function might result in some circular dependence, or to be more precise, it is more prone to circular dependencies.  
  
Regarding your comment on the "new" design: I think the big advantage is, within the implementation of some algorithm, that we are now able to call freely/easily other algorithms via their free functions. But it is not obvious to me that it would be "safe" to call the free function of the algorithm whose implementation we are within.  
  
So in order to be on the safe side, and unless there is a very compelling reason I prefer to adhere to the following guidelines:  
- Within the implementation of some algorithm, it is okay to call any other algorithm.  
- Within the implementation of some algorithm, use only the dispatch mechanism (as opposed to the free function) to call other instances of the algorithm.  
  
Hope this makes sense.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2014-10-15 06:12:26 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18876123  

Essentially the same situation as your previous comment.  
See answer there. It applies here as well.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2014-10-15 06:35:33 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18876730  

I am calling the free function on a point and a ring. Assuming that `bg::within` is implemented for both CW and CCW rings (which it is), the orientation of the ring should not be an issue.  
  
AFAIC, the call is correct, it works in the unit tests, and especially in this case, I would not consider changing `bg::within` for one of `bg::intersects` or `bg::covered_by`.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2014-10-15 09:41:03 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18883658  

Of course it should work for both orientations, this is my Point. But if `within()` is called for a hole, `true` should be returned if a Point is in the interior of a Polygon so "outside" a hole. But in this condition block there is a comment:  
  
```  
// the point is inside a polygon hole, so its distance  
// to the polygon its distance to the polygon's  
// hole boundary  
```  
  
AFAIU for this `within()` should return `false`. Furthermore if a Point is on the boundary the distance will also be equal to `0` so `covered_by()` might be used instead of `within()`. Hence `!covered_by()`. So am I mistaken or are Rings' Points reversed somehow?

---

## Comment 16

> Username: awulkiew  
> Created_at: 2014-10-15 10:09:09 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18884816  

Similar but not the same. The previous one was about a possible bug. This is just an optimization. Basically if a Point is on the boundary the distance should also be `0`. But I guess the Point might be "slightly" outside but still `covered_by()`/`intersects()` would return true because some EPS is taken into account. So in my version distance between such Geometries would be `0`. In your version it's some very small number which is more acurate. Unfortunately, AFAIU your version would also return non-0 distance for a Point overlapping the interior very close to the boundary of a Polygon, for which `within()` would return false (since this Point would be detected on the boundary). Like this:  
  
![text4702-5-8](https://cloud.githubusercontent.com/assets/1226951/4643724/790733e2-5452-11e4-90c4-1808bcdc9f7a.png)  
  
I don't know which one is better, `covered_by()` is at least "stable". If we had some method of disabling EPS checks or setting EPS to 0 (but only for some of the checks - the "perpendicular" to segments) it could be used here with `covered_by()` and it'd probably give the best results. Maybe we should have such strategy for the purpose of calculating `distance()`?

---

## Comment 17

> Username: mkaravel  
> Created_at: 2014-10-15 10:35:22 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18885773  

When calling `geometry::within(point, *it)`, `within` has no idea that the passed ring is a polygon hole; so it treats `*it` as a ring, and computes accordingly: if the point is fully inside the ring it returns `true`.  
  
If you take a look at how `within(point, polygon)` is implemented, you will realize that the same thing is happening: if a point is within the exterior ring, the algorithms checks, for each interior ring, if the point is outside, within or on the boundary of the interior ring. If an interior ring is found that has the point on its boundary or within it, then the point is returned as not within the polygon, otherwise the point is within the polygon. The fact that the interior rings are holes of a polygon is irrelevant here.

---

## Comment 18

> Username: mkaravel  
> Created_at: 2014-10-15 10:44:19 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18886132  

I buy the "stability" argument. I will change the call to `within()` by a call to `covered_by()`.

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2014-10-15 12:44:07 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18890591  

You mention in the introduction that the calls to intersects are completely gone.  
However, I see several of them... In segment/segment it is OK, but at this location I'm less sure about this.  
OK - this is not linear/linear, is that the reason?

---

## Comment 20

> Username: mkaravel  
> Created_at: 2014-10-15 12:50:02 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18890816  

Yes, this is the reason. The calls to `bg::intersects()` are gone for linear/linear geometries (where linear stands for linestring or multilinestring).

---

## Comment 21

> Username: awulkiew  
> Created_at: 2014-10-15 12:52:01 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18890894  

Yes, `within()` only does calculations accordingly to the orientation set for a Ring. If the stored Points has the same orientation (like in the exterior ring) then `within()` returns true for a Point inside that ring (in the interior - possible distance 0). But if the Points are in the reversed order wrt Ring's orientation then `within()` should return true for Point was outside the hole (in the interior of a Polygon - distance 0), not inside (in the exterior of a Polygon).  
  
I debugged it and `within()` returns true for a hole/Ring of CCW Polygon, so for Points oriented as CW, in the opposite direction (`polygon((-10 -10,10 -10,10 10,-10 10,-10 -10),(-5 -5,-5 5,5 5,5 -5,-5 -5))`.  
  
Is my reasoning wrong or does `within()` not work correctly?

---

## Comment 22

> Username: mkaravel  
> Created_at: 2014-10-15 13:41:01 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18893611  

Can you give a concrete example: a point and a polygon?  
  
I have considered the following program:  
  
```  
#include <cassert>  
  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
namespace bg = boost::geometry;  
  
typedef bg::model::point<double, 2, bg::cs::cartesian> point_type;  
typedef bg::model::ring<point_type, true> cw_ring_type;  
typedef bg::model::ring<point_type, false> ccw_ring_type;  
typedef bg::model::polygon<point_type, true> cw_polygon_type;  
typedef bg::model::polygon<point_type, false> ccw_polygon_type;  
  
  
int main()  
{  
    cw_ring_type cw_ring;  
    bg::read_wkt("POLYGON((0 0,0 10,10 10,10 0,0 0))", cw_ring);  
    assert( bg::is_valid(cw_ring) );  
  
    ccw_ring_type ccw_ring;  
    bg::read_wkt("POLYGON((0 0,10 1,10 10,0 10,0 0))", ccw_ring);  
    assert( bg::is_valid(ccw_ring) );  
  
    point_type p;  
    bg::read_wkt("POINT(1 1)", p);  
  
    std::cout << std::boolalpha;  
  
    std::cout << "CW ring: " << bg::wkt(cw_ring) << std::endl;  
    std::cout << "point  : " << bg::wkt(p) << std::endl;  
    std::cout << "within : " << bg::within(p, cw_ring) << std::endl;  
  
    std::cout << std::endl;  
  
    std::cout << "CCW ring: " << bg::wkt(ccw_ring) << std::endl;  
    std::cout << "point  : " << bg::wkt(p) << std::endl;  
    std::cout << "within : " << bg::within(p, ccw_ring) << std::endl;  
  
    std::cout << std::endl;  
    std::cout << std::endl;  
    std::cout << std::endl;  
  
    cw_polygon_type cw_polygon;  
    bg::read_wkt("POLYGON((0 0,0 10,10 10,10 0,0 0),(1 1,9 1,9 9,1 9,1 1))", cw_polygon);  
    assert( bg::is_valid(cw_polygon) );  
  
    ccw_polygon_type ccw_polygon;  
    bg::read_wkt("POLYGON((0 0,10 1,10 10,0 10,0 0),(1 1,1 9,9 9,9 1,1 1))", ccw_polygon);  
    assert( bg::is_valid(ccw_polygon) );  
  
    bg::read_wkt("POINT(5 5)", p);  
  
    std::cout << std::boolalpha;  
  
    std::cout << "CW polygon: " << bg::wkt(cw_polygon) << std::endl;  
    std::cout << "point  : " << bg::wkt(p) << std::endl;  
    std::cout << "within (polygon) : "  
              << bg::within(p, cw_polygon)  
              << std::endl;  
    std::cout << "within (hole) : "  
              << bg::within(p, bg::interior_rings(cw_polygon)[0])  
              << std::endl;  
  
    std::cout << std::endl;  
  
    std::cout << "CCW polygon: " << bg::wkt(ccw_polygon) << std::endl;  
    std::cout << "point  : " << bg::wkt(p) << std::endl;  
    std::cout << "within (polygon): "  
              << bg::within(p, ccw_polygon)  
              << std::endl;  
    std::cout << "within (hole): "  
              << bg::within(p, bg::interior_rings(ccw_polygon)[0])  
              << std::endl;  
  
    return 0;  
}  
```  
  
When I run it I get the following output:  
  
```  
CW ring: POLYGON((0 0,0 10,10 10,10 0,0 0))  
point  : POINT(1 1)  
within : true  
  
CCW ring: POLYGON((0 0,10 1,10 10,0 10,0 0))  
point  : POINT(1 1)  
within : true  
  
  
  
CW polygon: POLYGON((0 0,0 10,10 10,10 0,0 0),(1 1,9 1,9 9,1 9,1 1))  
point  : POINT(5 5)  
within (polygon) : false  
within (hole) : true  
  
CCW polygon: POLYGON((0 0,10 1,10 10,0 10,0 0),(1 1,1 9,9 9,9 1,1 1))  
point  : POINT(5 5)  
within (polygon): false  
within (hole): true  
```  
  
which is exactly what I want. So what am I missing? What is the problem you refer to?

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2014-10-15 13:42:40 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18893710  

These two variables (rtree_value_min and qit_min) are never used (they are apparently moved to range_to_range)

---

## Comment 24

> Username: barendgehrels  
> Created_at: 2014-10-15 13:45:08 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18893887  

I've no clue what cf stands for. Comparable something? Can you rename it?

---

## Comment 25

> Username: barendgehrels  
> Created_at: 2014-10-15 13:47:51 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18894089  

Is this ever false? Is there still a reason to keep it? Do we have an implementation without RTree-of-segments too? I would prefer to have one, unless there are good reasons

---

## Comment 26

> Username: awulkiew  
> Created_at: 2014-10-15 14:02:09 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18894961  

I thought that the result of `within()` could be affected by the order of the input. Similar to the way how `area()` is affected, the result is negative for Rings containing Points with different ordering. So `within()` could return the opposite result for differently ordered Rings. But it seems that at least the default `winding` strategy is not affected by the order. There is also not documented `ordered_winding` changing the result for one of the orderings somehow. So I think the result depends on the strategy. I'm wondering if the behavior of the `winding` strategy is the same for non-cartesian coordinate systems, it should be.  
  
So, sorry for raising the alarm.  
  
Btw, so AFAIU here the use of `within()` shouldn't be changed for `covered_by()` for "stability" since it's already ok.

---

## Comment 27

> Username: mkaravel  
> Created_at: 2014-10-15 16:38:17 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18905457  

Done.

---

## Comment 28

> Username: mkaravel  
> Created_at: 2014-10-15 16:38:45 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18905481  

Done.

---

## Comment 29

> Username: mkaravel  
> Created_at: 2014-10-15 16:39:06 UTC  
> Updated_at: 2014-10-16 15:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18905497  

Done. Both removed.

---

## Comment 30

> Username: mkaravel  
> Created_at: 2014-10-16 15:30:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18963253  

`cf` stands for _closest features_. Renamed.

---

## Comment 31

> Username: mkaravel  
> Created_at: 2014-10-16 15:31:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#discussion_r18963359  

In the current implementation it is always true by default. I have done some benchmarks and in the vast majority of cases, using the segments is a superior option.  
AFAIC, there is not real reason to keep the old code. So I have removed it.

---

## Comment 32

> Username: mkaravel  
> Created_at: 2014-10-16 15:32:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/161#issuecomment-59380829  

If I am not mistaken I have made all the changes suggested in the review.  
Adam and Barend many thanks for your feedback.  
I believe the PR is ready for merging.

---
