# #156 Bug with CCW polygons with holes [Closed]

> Username: zerebubuth  
> Created at: 2014-10-11 16:59:00 UTC  
> Updated at: 2014-10-13 03:57:32 UTC  
> Closed at: 2014-10-13 03:57:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/156  

I first found this when a polygon with a hole was not disjoint from another polygon which fit within the hole. It seemed like the issue may have been that only outer rings were used to provide sample points for contains-testing in the disjoint algorithm, but this did not solve the issue. Upon further investigation, it seemed that `point_on_surface` was ignoring inner rings of CCW polygons due to a CW assumption in `get_intruders` and `right_turn` used as part of the extrema-finding algorithm.  
  
I can't say I fully understand the algorithm, and the "fix" I have provided in 924de9b1792356e68e3003b9bb991d8c52ba5a07 is based more on intuition than mathematics. However, I hope that some of this may be of use in fixing the problem for real.  
  
Thanks!

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-10-11 21:36:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/156#issuecomment-58765360  

Thanks for finding the bugs and proposing a fix!  
  
So we have 3 bugs here:  
1. point_on_surface() not working for CCW Polygons.  
2. numerical erros in point_in_surface() for integral coordinates  
3. disjoint() not working properly for holes   
  
ad1. FYI, the bug is also fixed if bg::reversible_view or bg::detail::normalize_view is used for Rings in extreme_points(). Using views is more clear and probably safer since this may be not the only place where the CW specific order was assumed.  
  
On the down side, I think that in general using views could be slower than fixes such as the one proposed on some platforms. With reversible_view CCW Polygon's elements must be traversed in reverse order and the processor may be unable to prefetch chunks of data properly in this case. So it's possible that e.g. setops and relops may work faster for CW Polygons than for CCW. Just a thought.  
  
ad2. That's obvious.  
  
ad3. Correct me if I'm wrong, I may be missing something but the fix for `disjoint()` won't work in all cases (neither the original as you proved). Let's say that we have a case like the one below. The points was returned by the point_on_surface() calls (there is no guarantee where they'd be placed).  
  
![path3814](https://cloud.githubusercontent.com/assets/1226951/4604051/1fcc4c06-5185-11e4-9b70-dd49013106bc.png)  
  
There is no intersection points of boundaries so those `rings_containing()` checks are called in `detail::disjoint::general_areal`. The green PoS is not within the blue Polygon and the blue PoS is not within the green Polygon. disjoint() would return false in this case which is invalid.  
  
Btw, in the original algorithm `for_each_range` doesn't check the inner rings (instead there is nice TODO there). So effectively it works more or less like your version. The only difference is that the PoS is calculated for an exterior ring instead of for the "whole" Polygon, so it may be placed in a hole. But this doesn't change the fact that in your version it may also be placed anywhere inside a Polygon.  
  
I think that the fix should replace `point_on_surface()` with `point_on_boundary()`. And we should probably also recheck other calls of this function in the library.  
  
As a side note regarding the PR and commits (for the future):  
1. Bug fixes of different algorithms or some parts of the library should probably be divided into separate PRs  
2. Please put in commit messages a name of an algorithm or some other name/id related with the change, in square brackets, e.g.:  
[point_on_surface] Do multiply & ....  
[disjoint] Use point on surface...  
(see: https://github.com/boostorg/geometry/commits/develop)

---

## Comment 2

> Username: zerebubuth  
> Created_at: 2014-10-13 03:57:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/156#issuecomment-58841886  

I've split this into #157, #158 and #159.  
  
Unfortunately, I wasn't able to get `bg::reversible_view` to work in #158, and I'm not sure why. Perhaps I was not using it correctly.

---
