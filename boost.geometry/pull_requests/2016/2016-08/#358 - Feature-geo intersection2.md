# #358 Feature/geo intersection2 [Merged]

> Username: awulkiew  
> Created at: 2016-08-16 03:57:06 UTC  
> Updated at: 2017-03-20 14:27:40 UTC  
> Merged at: 2017-03-01 21:27:52 UTC  
> Closed at: 2017-03-01 21:27:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/358  

This PR adds geographic formulas:  
- conversions geographic <-> cartesian 3d  
- projection of 3d point on spheroid into XY plane  
- intersection of 3d ray/line and spheroid   
- intersection of great elliptic arcs  
- alternative intersection of arcs on ellipsoid.  
  
Intersection of great elliptic arcs is similar to the spherical intersection. First the intersection of 3d planes is found (a line), then the intersection of this line and spheroid is found. The planes are calculated from two segment endpoints and the center of the spheroid or origin of the coordinate system.  
  
The alternative solution calculates the third, origin point differently. It first finds the points on XY plane corresponding to the endpoints (projected along the line perpendicular to the surface of spheroid and intersecting XY plane at angle being geodetic latitude). Then a midpoint between these two points lying on XY plane is calculated. This point is the 3rd point lying on a plane containing a segment. This plane is intersecting the XY plane at a slightly greater angle than the plane intersecting the center of the coordinate system, therefore better approximates the shortest path.  
  
These are the example results (in degrees):  
  
```  
segment a:            { 1, 1 },{ 0, 0 }  
segment b:            { 0, 1 },{ 1, 0 }  
karney gnomonic:      { 0.5000000000000000, 0.5000573755188484 }  
vincenty gnomonic:    { 0.5000000000000000, 0.5000573755188470 }  
thomas gnomonic:      { 0.5000000000000000, 0.5000573755109839 }  
karney sjoberg:       { 0.5000000000000000, 0.5000573755505008 }  
vincenty sjoberg:     { 0.5000000000000000, 0.5000573755501669 }  
thomas sjoberg:       { 0.4999999999999996, 0.5000573661218464 }  
andoyer sjoberg:      { 0.4999999999999999, 0.5000545856093679 }  
alternative elliptic: { 0.5000000000000001, 0.5000573754962017 }  
great elliptic:       { 0.4999999999999999, 0.5000571197534014 }  
```  
  
In the above case (157 km segment) the alternative solution is comparable to `thomas gnomonic` and `vincenty sjoberg`. Taking `karney gnomonic` as reference the error is 0.002 mm, while for formulas mentioned above 0.0009 mm and 0.003 mm respectively. For great elliptic arcs the error is 28 mm.  
  
Further from equator for segment of similar length the results are similar.  
  
These are the example results for longer segment (in degrees):  
  
```  
segment a:            {10, 10}, {55, 55},  
segment b:            {10, 55}, {55, 10},  
karney gnomonic:      {32.5000000000000000, 40.9904993376446924},  
vincenty gnomonic:    {32.5000000000000000, 40.9904993376599620},  
thomas gnomonic:      {32.4999999999999929, 40.9906923751817160},  
karney sjoberg:       {32.5000000003269562, 40.9904993373937359},  
vincenty sjoberg:     {32.5000000003792167, 40.9904993374044864},  
thomas sjoberg:       {32.5000001386361674, 40.9904992577855580},  
andoyer sjoberg:      {32.4997696687483781, 40.9905358424692068},  
alternative elliptic: {32.5000000000000000, 40.9873411744712755},  
great elliptic:       {32.4999999999999929, 40.9689452810313171}  
```  
  
For longer segments (above is 6355 km long) the accuracy is lower. In this case it's lower than `thomas gnomonic` and `andoyer sjoberg`. Taking `karney gnomonic` as reference the error is 350 m and for the mentioned formulas it's 21 m and 20 m respectively. For great elliptic arcs the error is 2393 m.  
  
It's possible that the alternative solution could be further improved. The 3rd plane point could be altered/calculated differently, e.g. not use the average midpoint but take the latitudes of endpoints into account to weight the position of the midpoint or take the difference of longitudes into account, etc.  
  
The times of calculation of 10000 intersections using each method are listed below (MSVC14, times in seconds):  
  
```  
karney gnomonic:      1.552  
vincenty gnomonic:    0.163  
thomas gnomonic:      0.123  
karney sjoberg:       0.090  
vincenty sjoberg:     0.030  
thomas sjoberg:       0.018  
andoyer sjoberg:      0.017  
alternative elliptic: 0.009  
great elliptic:       0.006  
```

---

## Comment 1

> Username: awulkiew  
> Created_at: 2016-08-24 16:09:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/358#issuecomment-242121291  

In the recent portion of commits I divided the tools related to formulas using elliptic arcs into smaller reusable parts. I added two intersection strategies, both using elliptic arcs. For this purpose I modified the spherical strategy (and renamed it) to take a `CalcPolicy` defining several key calculations. So now this strategy is used as a base of spherical and two geographic strategies.  
  
The experiments show that the strategy using great elliptic arcs gives the same results as spherical strategy. AFAIU this is correct.  
  
Regarding the experimental elliptic strategy there are problems with consistency of side calculation because currently the planes corresponding to segments are calculated WRT single segment and may not cross the origin of the coordinate system. So points of one segment may lie on another segment's arc but in the same time e.g. one of the points of the other segment may not lie on the first segment's arc. There is workaround for this but at the moment it is not possible to implement it in side strategy because it doesn't have the information about the other segment.  
  
So currently the default geographic strategy was left intact, i.e. spherical strategy is used because it is faster than the one representing segments as great elliptic arcs and returns the same results.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-01-18 12:48:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/358#issuecomment-273466759  

I solved the issues mentioned above. In sjoberg geodesic intersection formula first the spherical intersection is calculated and then iterative method(s) used to find the geographic result. First try is done with the newton method [Sj02] but because it may not converge properly then the next try is done with the method proposed by Sjoberg [Sj07]. It's because newton method converges faster but is less reliable.  
  
So I think this PR is ready for review. It adds strategies which could be used in geographic CS but in order to use them various algorithms has to be modified to actually take and use the intersection strategy (relational and set operations). But since this PR is quite big already I'd prefer to do it in a separate one.  
  
[Sj02] Lars E. Sjoberg, Intersections on the sphere and ellipsoid, 2002  
[Sj07] Lars E. Sjoberg, Geodetic intersection on the ellipsoid, 2007

---

## Review 3 [Commented]

> Username: vissarion  
> Created_at: 2017-02-20 16:09:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/358#pullrequestreview-22788920  

Thanks Adam for this PR. Some comments follow:  
  
Is sjoberg_intersection_spherical_02 different than great circle/ellipse intersection?   
  
Clairaut constant could be a formula since it is used in many places of the library.  
  
The actual algorithm you implement is not proposed by Sjoberg, but is using methods proposed by Sjoberg. AFAIU it is a new finding that the one method is fast and not reliable while the other is slower and more reliable.   
  
When checking for disjointness of two segments, should this be implemented inside disjoint(segment,segment) algorithm?  
  
Intersection of great elliptic arcs is correct to be the same as the one of great circle arcs since the computation is performed in cartesian 3d space without taking into consideration the sphere or the ellipsoid.

📁 include/boost/geometry/strategies/geographic/geodesic_intersection.hpp

```diff
  52 |+ // picking latitudes closer to 0
  53 |+ // etc.
  54 |+ 
```

> Username: vissarion  
> Created_at: 2017-02-20 15:46:24 UTC  
> Updated_at: 2017-02-27 18:31:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/358#discussion_r102045023  

What does this mean?

> Username: awulkiew  
> Created_at: 2017-02-20 16:52:36 UTC  
> Updated_at: 2017-02-27 18:31:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/358#discussion_r102058244  

Regarding longitudes. If we had a segment far from 0, e.g. `SEGMENT(170 0, 171 1)` then we could move it to `SEGMENT(0 0, 1 1)` or `SEGMENT(-0.5 0, 0.5 1)` calculate what's needed (e.g. azimuth or intersection point with other segment/geodesic) and then move back the result. Theoretically this could give more accurate result and more importantly it'd give result consistent with similar cases no matter where they were placed on the globe.  
  
Similar with latitude, it could be the case that if we had two endpoints one close to 0 and the other one far from 0 then if we used the closer one as the reference point of a geodesic then the accuracy of the result of the formula.

---

📁 include/boost/geometry/strategies/geographic/geodesic_intersection.hpp

```diff
 333 |+             {
 334 |+                 return collinear_one_degenerted<Policy, calc_t>(a, true, b1, b2, a1, a2, res_b1_b2, res_b1_a1, is_b_reversed);
 335 |+             }
```

> Username: vissarion  
> Created_at: 2017-02-20 15:51:20 UTC  
> Updated_at: 2017-02-27 18:31:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/358#discussion_r102045972  

collinear_one_degenerted --> collinear_one_degenerated  
  
same everywhere in the file


---

## Comment 4

> Username: awulkiew  
> Created_at: 2017-02-20 17:25:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/358#issuecomment-281137659  

> Is sjoberg_intersection_spherical_02 different than great circle/ellipse intersection?  
  
It's great circle intersection but calculated using formula proposed in [Sj02], there is also an alternative one proposed in [Sj07] and the one we're using in spherical intersection and side strategies, i.e. 3d cartesian ECEF.  
  
> Clairaut constant could be a formula since it is used in many places of the library.  
  
Yes, though Sjoberg is not consistent regarding the signs of the constant (based on azimuth) so it's not clear to me what should be the exact result. But indeed maybe it could be encapsulated in a formula as absolute value or raw value and then the sign adjusted if needed. However then the definition of "Clairaut constant" woudl be different in Sjoberg and Boost.Geometry, though it already is inconsistent anyway in [Sj02] vs [sj07].  
  
> The actual algorithm you implement is not proposed by Sjoberg, but is using methods proposed by Sjoberg. AFAIU it is a new finding that the one method is fast and not reliable while the other is slower and more reliable.  
  
Yes, it's a hybrid, robust approach. Do you think that the name of the formula should be changed?  
  
> When checking for disjointness of two segments, should this be implemented inside disjoint(segment,segment) algorithm?  
  
disjoint(segment, segment) uses the Segment/Segment intersection strategy, so no, the algorithm should only call the strategy and check the result, as it is now. For now spherical one is used by default. In this PR I didn't enable this new geodesic strategy as the default strategy.  
  
> Intersection of great elliptic arcs is correct to be the same as the one of great circle arcs since the computation is performed in cartesian 3d space without taking into consideration the sphere or the ellipsoid  
  
The sphere/spheroid is taken into account during the conversion from and to ECEF/cartesian. The 3D vectors are different after converting from spherical and geographic. So it isn't obvious that the result would be the same.  
  
In addition to this, in 2D spherical/geographic the conversion is direct but in 3D geographic (with height) it is required to use some non-direct method (e.g. iterative solution) to convert back from 3D ECEF/cartesian to 3D geographic. See:  
http://www.navipedia.net/index.php/Ellipsoidal_and_Cartesian_Coordinates_Conversion  
https://github.com/boostorg/geometry/pull/358/files#diff-be03f632ea4281ab4d575b4409af7f7aR116  
However I haven't investigated it further because we're dealing with 2D spherical/geographic for now.

---

## Comment 5

> Username: vissarion  
> Created_at: 2017-03-01 10:33:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/358#issuecomment-283304345  

Thanks, I am ok with merging.

---
