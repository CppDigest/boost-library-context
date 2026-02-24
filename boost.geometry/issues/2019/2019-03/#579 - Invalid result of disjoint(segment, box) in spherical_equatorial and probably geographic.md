# #579 - Invalid result of disjoint(segment, box) in spherical_equatorial and probably geographic. [Closed]

> Username: awulkiew  
> Created at: 2019-03-25 18:56:28 UTC  
> Updated at: 2019-03-27 08:19:53 UTC  
> Closed at: 2019-03-27 08:19:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/579  

When the segment is going along meridian and is pointing south, e.g.:  
  
    using coordinate_system = boost::geometry::cs::spherical_equatorial<boost::geometry::degree>;  
    using lonlat = boost::geometry::model::point<double, 2, coordinate_system>;  
    using box = boost::geometry::model::box<lonlat>;  
    using segment = boost::geometry::model::segment<lonlat>;  
  
    boost::geometry::disjoint(box{ { 10, 10 },{ 20, 20 } },  
                              segment{ { 12, 2 }, { 12, 1 } });  
  
Internally in `disjoint_segment_box_sphere_or_spheroid::apply` envelope of a segment is calculated at line 162 and the result is wrong, i.e. `box{{12, 1}, {12, 90}}`.  
  
AFAIU it's because above `alp1` is calculated and passed into the `envelope_segment_impl` for the segment pointing south (`alp1` is `pi`) and inside `envelope_segment_impl::special_cases` the latitudes are reversed because `lon1 == lon2` at line 264. Now the segment is pointing north and these reversed coordinates are passed into the azimuth strategy to calculate reverse azimuth (`alp2` is `0`). Obviously both azimuths are incompatible.

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-03-25 18:58:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/579#issuecomment-476333785  

Detected while working on PR: https://github.com/boostorg/geometry/issues/466  
and using disjoint Segment/Box in implementation of disjoint Areal/Box.

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-03-25 19:16:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/579#issuecomment-476339878  

I added failing cases: https://github.com/boostorg/geometry/commit/30ebfb8cd53ce5041fc55ae5f5a23a0146a5805b

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-03-25 19:51:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/579#issuecomment-476351628  

In `special_cases` the coordinates can be swapped in other cases too, like `is_pole1 && is_pole2` or `lon1 > lon2`. There is no guarantee that the azimuth passed from the outside will be consistent with this function and reverse azimuth calculated afterwards. So I see 3 solutions:  
  
1. Implement some normalization function the same for all spherical and geographic coordinates and call it at the beginning of all non-cartesian calculations in the library, before passing the coordinates to any formula. This is horribly error-prone and would cause trouble.  
  
2. Assume that the azimuth passed from the outside is calculated for the original segment and either recalculate it when the original coordinates are modified or based on the condition modify azimuth to match the swapped coordinates. This would be done more or less half of the time (lon1 > lon2).  
  
3. Remove the azimuth parameter since the reverse azimuth is calculated anyway and the difference between calculating one or both is small, at least in geographic. Is it different in spherical? Furthermore becasue this feature (passing azimuth from the outside) is used only in disjoint Seg/Box, nowhere else.  
  
Obviously 3 is the safest and easiest one but 2 would be ok too. But is it worth it? What was the reason behind it? Have some benchmarks shown that there is a difference in performance or was it premature optimization?

---

## Comment 4

> Username: awulkiew  
> Created at: 2019-03-25 20:50:07 UTC  
> Updated at: 2019-03-25 20:54:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/579#issuecomment-476371784  

I noticed yet another issue in `special_cases`. If the segment crosses antimeridian period is added to lon2 and these coordinates are passed into azimuth strategy. Technically these coordinates are not in valid range for strategies and formulas. Even if they can deal with longitude out of bounds increasing the value may cause some numerical issues.  
  
Do I understand correctly that all of these modifications are only to make it easier calculation of corners in `compute_box_corners`? That all formulas used internally (azimuth and vertex_latitude) are capable of dealing with these "special cases"? Or does `envelope_segment_call_vertex_latitude` expect a segment in some specific orientation? It seems that the only purpose of `special_cases` is to make the implementation of `compute_box_corners` easier but it could as well be done at the end.  
  
I'm wondering wouldn't it be simpler to calculate azimuths for the original segment, then if needed calculate vertex ~point~ latitude and update one of the points, and after that create box from 2 ~or 3~ points?

---

## Comment 5

> Username: vissarion  
> Created at: 2019-03-26 15:50:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/579#issuecomment-476714026  

Thanks for pointing out this issue. Regarding the swapping of coordinates part what you propose in 3 is the preferred choice, implemented here: https://github.com/boostorg/geometry/pull/580 Going choice 2 I think does not worth it since it adds code complexity (treatment of special cases) without increasing the efficiency of the code for the ordinary user (I have no benchmarks but the optimization with azimuths seems to have more sense for some strategies more than other, e.g. vincenty).

---

## Comment 6

> Username: awulkiew  
> Created at: 2019-03-26 16:52:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/579#issuecomment-476743665  

I'm fine with removing the parameter and corresponding functions and close this issue. This looks as premature optimization anyway and if there are some problems in the future we can always go back to this.
