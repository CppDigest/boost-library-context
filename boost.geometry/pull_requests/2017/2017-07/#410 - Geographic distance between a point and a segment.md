# #410 Geographic distance between a point and a segment [Merged]

> Username: vissarion  
> Created at: 2017-07-18 10:55:21 UTC  
> Updated at: 2017-11-02 10:12:49 UTC  
> Merged at: 2017-10-31 14:46:19 UTC  
> Closed at: 2017-10-31 14:46:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/410  

This PR proposes a formula for computing the geographic distance between a point and a segment. It also adds the respective strategy. This strategy will be used by point-box, box-box and general geometry-geometry distance computation in future PR(s).  
  
The distance is approximated by an iterative Newton method. The algorithm accepts as template parameters a direct and an inverse geodesic formula. Thus, its accuracy and performance depends on that choice. There is also a choice to compute the closest point on the input segment to the query point.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-07-18 13:30:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-316064003  

Why will it be used in Pt-Box and Box-Box distance computation? Box's horizontal edges aren't geodesics so in this case the distance cannot be calculated with Pt-Seg strategy and vertical edges are meridians so it's trivial to calculate the closest point on an edge and then call Pt-Pt distance strategy. The distance between vertices must be calculated with Pt-Pt distance strategy. Am I missing something?

---

## Comment 2

> Username: vissarion  
> Created_at: 2017-07-18 13:50:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-316069773  

Sure, for Pt-Box and Box-Box the strategy is called with a point and a meridian segment which is an easy case and there is no need for Newton iteration, so the strategy will be called but the formula not.

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2017-08-30 00:55:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/410#pullrequestreview-59421685  

📁 include/boost/geometry/formulas/distance_point_segment.hpp

```diff
  35 |+         typename Inverse_type_azimuth,
  36 |+         typename Inverse_type_distance,
  37 |+         typename Direct_type,
```

> Username: awulkiew  
> Created_at: 2017-08-30 00:55:44 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r135948270  

I suggest to take direct and inverse formulas as class template instead of a type here:  
  
    template <typename, bool, bool, bool, bool ,bool> class Inverse,  
    template <typename, bool, bool, bool, bool> class Direct,  
  
And then internally define types passing appropriate parameters:  
  
    typedef Inverse<CT, true, false, false, false, false> inverse_distance_type;  
    typedef Inverse<CT, false, true, false, false, false> inverse_azimuth_type;  
  
This way this formula would create inverse formula types able to calculate whatever is needed, so it wouldn't rely on a caller. Currently the caller has to know which results of inverse formula will be used internally and pass the correct type.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2017-08-30 01:05:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/410#pullrequestreview-59422785  

📁 include/boost/geometry/formulas/distance_point_segment.hpp

```diff
  80 |+                         Spheroid const& spheroid,
  81 |+                         CT earth_radius =
  82 |+                            geometry::srs::sphere<CT>().get_radius<1>())
```

> Username: awulkiew  
> Created_at: 2017-08-30 01:05:57 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r135949292  

What's the point of passing WGS84 mean radius here? AFAIU it's redundant and forces the user to pass correct radius, consistent with spheroid. I guess it's for calculating some initial values before iteration. But shouldn't it simply be calculated from passed spheroid internally?


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2017-08-30 01:18:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/410#pullrequestreview-59424144  

📁 include/boost/geometry/strategies/geographic/distance_cross_track.hpp

```diff
  66 |+     typename CalculationType = void
  67 |+ >
  68 |+ class cross_track_geo
```

> Username: awulkiew  
> Created_at: 2017-08-30 01:18:49 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r135950478  

The naming scheme for geographic strategies should be consistent. Since the intersection strategy is named `geographic_segments` I propose to rename this strategy e.g. to `geographic_cross_track`.


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2017-08-30 01:30:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/410#pullrequestreview-59425329  

📁 include/boost/geometry/strategies/geographic/intersection.hpp

```diff
 119 |     }
 120 | 
 121 |-     template <typename Geometry>
```

> Username: awulkiew  
> Created_at: 2017-08-30 01:30:06 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r135951596  

What's the reason of changing the interface of geographic intersection strategy? This way it has interface inconsistent with other intersection strategies. Plus it cannot be passed into algorithms (`equals()`) getting distance strategy from intersection strategy without changing the algorithms as well.


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2017-08-30 01:34:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/410#pullrequestreview-59425857  

📁 include/boost/geometry/strategies/geographic/parameters.hpp

```diff
  27 | struct andoyer
  28 | {
  29 |+     //TODO: this should be replaced by an andoyer direct formula
```

> Username: awulkiew  
> Created_at: 2017-08-30 01:34:46 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r135952087  

Right, implementing andoyer direct formula would be preferable to using thomas.

> Username: vissarion  
> Created_at: 2017-09-12 11:12:25 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r138317302  

This needs some research. I propose to do this in a separate PR. It is expected that using thomas direct it should be a bit slower and with small deviations in the computed result compared to the future andoyer direct formula.


---

## Comment 8

> Username: vissarion  
> Created_at: 2017-09-12 11:14:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-328821422  

Thanks for the review comments, I did some changes addressing them.

---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2017-09-12 19:02:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/410#pullrequestreview-62233181  

📁 include/boost/geometry/formulas/distance_point_segment.hpp

```diff
  84 |+     {
  85 |+         CT earth_radius = (CT(2) * spheroid.get_radius<1>()
  86 |+                            + spheroid.get_radius<2>()) / CT(3);
```

> Username: awulkiew  
> Created_at: 2017-09-12 19:02:06 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r138437794  

I just added `mean_radius()` formula to develop which could be used here, also the variable could be const. But this could be changed later after the PR is merged.

> Username: vissarion  
> Created_at: 2017-09-13 10:21:53 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r138580447  

I used the mean_radius() formula here, thanks. A side remark: is there a reason to call the argument of mean_radius() "geometry"; as far as I understand it is a coordinate system reference model, i.e. a sphere or spheroid?

> Username: awulkiew  
> Created_at: 2017-09-13 10:43:15 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r138584531  

It's just a general name for parameter of multiple types. It could've been called `Model` as well. But a geometry could also be passed into this function, like, e.g. nsphere from extensions or some n-dimensional ellipsoid in the future, though very unlikely we'll have that.


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2017-09-12 19:04:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/410#pullrequestreview-62233796  

📁 include/boost/geometry/formulas/distance_point_segment.hpp

```diff
  41 |+ public:
  42 |+ 
  43 |+     typedef Inverse<CT, true, false, false, true, true> inverse_distance_type;
```

> Username: awulkiew  
> Created_at: 2017-09-12 19:04:33 UTC  
> Updated_at: 2017-09-21 12:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#discussion_r138438288  

Here the name doesn't fully reflect the parameters since besides distance quantities are calculated but I guess that's fine.


---

## Comment 11

> Username: awulkiew  
> Created_at: 2017-09-12 19:05:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-328950738  

Thanks! I'm ok with merging if you don't plan to add anything else to this PR.

---

## Comment 12

> Username: vissarion  
> Created_at: 2017-09-13 10:23:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-329125270  

Added some changes related to last comments. I am ok now.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2017-09-13 10:50:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-329131428  

The last commit is very big and contains wierd changes. Would it be possible to make it in a clean way? I.e. reset the HEAD to the previous one (4e1e18f), merge the latest develop in your branch and after that make `mean_radius()` commit and force-push?

---

## Comment 14

> Username: vissarion  
> Created_at: 2017-09-19 12:23:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-330521932  

Still working on this PL, some comments below:  
  
1. Regarding comment above on distance point to meridian segment, note that it is not trivial to compute the closest point. The misleading comes from the fact that parallels create 90 degree angles with meridians so one might think that the closest point on a meridian segment has the same latitude as the query point. But parallels are not shortest paths apart from equator so the closest point is always closer to the pole. This is the case for both spherical and geographic CS.  
  
2. There was an issue in spherical cross_track distance computation algorithm that is fixed here. There was a non symmetric behaviour. That is, given for example a parallel segment (a,b)--(c,b) the distance from point (a,b) is not exactly the same as from point (c,b). The longer the segment the larger the error. The issue also holds for non-parallel segments. This is fixed by using the correct azimuths to compute the projection of the point on (or out of) the segment. Also formulas are used instead of strategies for azimuths that should improve performance a bit.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2017-09-19 13:07:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-330533113  

1. You're correct, this is not trivial. Though since we know the longitude of the result I guess we can use this information in iterative solution which could simplify it.  
  
2. Thanks! So I guess `course` include and the function itself could be removed now.

---

## Comment 16

> Username: vissarion  
> Created_at: 2017-09-19 14:32:00 UTC  
> Updated_at: 2017-09-19 14:32:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-330558796  

1. This makes sense and needs some investigation.  
2. calls to `course` has been removed from the whole library, I kept the function for backward compatibility; but I have no idea if this is used by any users.

---

## Comment 17

> Username: vissarion  
> Created_at: 2017-10-26 12:07:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-339644824  

@awulkiew I will create a new PL with a solution for (1). So I am done with this PL for now.

---

## Comment 18

> Username: awulkiew  
> Created_at: 2017-10-31 14:45:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/410#issuecomment-340785028  

Thanks!

---
