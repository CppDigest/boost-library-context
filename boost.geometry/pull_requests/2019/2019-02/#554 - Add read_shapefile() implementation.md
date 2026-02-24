# #554 [extensions][io] Add read_shapefile() implementation. [Merged]

> Username: awulkiew  
> Created at: 2019-02-07 00:16:20 UTC  
> Updated at: 2019-08-14 14:32:26 UTC  
> Merged at: 2019-08-14 14:32:26 UTC  
> Closed at: 2019-08-14 14:32:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/554  

This PR adds simple `read_shapefile()` IO reading `*.shp` passed as STL IStream and filling a mutable Range of Geometries suitable for it:  
- range of points, incl. multi-point  
- range of multipoints  
- range of linestrings, incl. multi-linestring  
- range of multi-linestrings  
- range of polygons, incl. multi-polygon  
  
It doesn't depend on external libraries, it only loads geometrical data from `*.shp` file so it's lightweight.  
  
See: [shapefile technical description](https://www.esri.com/library/whitepapers/pdfs/shapefile.pdf).  
  
Example:  
  
    using point_geo = bg::model::point<double, 2, bg::cs::geographic<bg::degree>>;  
    using multi_point_geo = bg::model::multi_point<point_geo>;  
  
    // http://www.naturalearthdata.com/downloads/10m-cultural-vectors/  
    std::ifstream ifs1("ne_10m_populated_places.shp", std::ifstream::binary);  
    std::ifstream ifs2("ne_10m_airports.shp", std::ifstream::binary);  
  
    multi_point_geo red, green;  
  
    bg::read_shapefile(ifs1, red);  
    bg::read_shapefile(ifs2, green);  
  
Result:  
  
![image](https://user-images.githubusercontent.com/1226951/52382307-f5cdf800-2a74-11e9-8725-17aea5f1c2ad.png)

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2019-02-07 00:34:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/554#pullrequestreview-200876345  

I have only skimmed the code, but it is neat and a good idea.

---

## Comment 2

> Username: vissarion  
> Created_at: 2019-02-07 10:58:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461374439  

Thanks! I am ok with merging.   
  
Could you please explain why you need the third case in your description (range of multi-linestrings)? Is not part of case 2?

---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-02-07 11:35:18 UTC  
> Updated_at: 2019-02-07 14:26:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461384201  

> Could you please explain why you need the third case in your description (range of multi-linestrings)? Is not part of case 2?  
  
It's because shp file contains a number of records of the same type. In case of linear geometries these records are of type *polyline*. A polyline can contain some number of parts. So if one wants to represent a range of linestrings then in the shp these will be polylines containing 1 part each. However they could have more parts in which case they would be multi-linestrings. So I decided to leave the decision to the user of the library.  
  
I forgot to add range of multi-points which can be expressed in shp file directly.  
  
~There is no multi-polygon type, hence no range of multipolygons.~ But this also means that if you put these polygons in BG multi-polygon it may be invalid because they may touch with edges.  
  
EDIT: The above is invalid shp polygon can contain any number of outer and inner rings so one have to create polygons based on the ordering. This is not done right now.  
  
There is also multi-patch which AFAIU is 3d mesh so this is not supported.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2019-02-07 11:36:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461384605  

I added link to technical description in case you wanted to check it out.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2019-02-07 14:24:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461439389  

It seems that *shp* polygon may contain any number of outer and inner rings in unspecified order. This means that my previous comment about multi-polygons is invalid. This also means that a polygon should be loaded, the orientation of all of the rings checked and inner rings assigned to outer rings.  
  
Side question: @vissarion we know that currently geographic area strategy may be innacurate for small polygons (https://github.com/boostorg/geometry/issues/541) but is it only the value that is inaccurate or is the sign innacurate too? I'm asking because area is used to check the order of points. Can we rely on area in this case even for small polygons?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2019-02-07 18:10:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461536614  

Cool!  
Will review this weekend.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2019-02-07 19:48:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461570936  

@vissarion I already know that the result of area can be negative, e.g for this polygon:  
  
    POLYGON((9.8591674311151110 54.602813224425063,  
             9.8591651519791412 54.602359676428932,  
             9.8584586199249316 54.602359676428932,  
             9.8591674311151110 54.602813224425063))  
  
![image](https://user-images.githubusercontent.com/1226951/52436951-ea7ed900-2b15-11e9-93e4-05ab67e417b4.png)  
  
from Germany map (https://www.statsilk.com/maps/download-free-shapefile-maps) I get the following areas:  
  
    andoyer: -1564.3942501147367  
    thomas:   1152.6180716450961  
    vincenty: 1572.6048669007134  
  
What is interesting is that perimeters look close:  
  
    andoyer:  164.31866226050522  
    thomas:   164.31808513930372  
    vincenty: 164.31807105463110  
  
Area state values at the end of calculation with andoyer:  
  
    m_excess_sum = -3.8764088234160264e-11  
    m_correction_sum = 3.3157083816766998e-11  
  
One is spherical term and the other ellipsoidal correction. It seems that the error is caused by the fact that they are small and close to each other. Are we sure that the spherical calculation is fully consistent with ellipsoidal correction calculation? E.g. there are 2 versions of thomas (series expansion) one using reduced latitudes and one using raw latitudes and both have different quirks. The result in edge cases may heavily depend on what's exactly calculated in a formula. So my guess is that the spherical calculation in ellipsoidal `area_formulas` may be inconsistent with andoyer. Does it make sense? Are there different ways of calculating it?  
  
Are we sure that everything is ok with area?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2019-02-08 00:02:55 UTC  
> Updated_at: 2019-02-08 00:12:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461642137  

@vissarion @barendgehrels Because area is not reliable for checking the order of points in a ring I'm thinking about replacing the test based on area with a different one, based on sum of exterior angles. The sum of exterior angles of a ring should be `2pi`. My guess is that it's the same in non-cartesian CSes. This means that an expensive test based on area calculation can be replaced with a lot simpler test only calculating azimuths. The complexity is O(N) in both cases. Not only it should be a faster but it should also be more accurate and more consistent with side strategies, at least geographic one since it's based on exactly the same azimuth calculation.  
  
I've implemented a simple geographic version and it solves the problematic case mentioned above.  
  
What do you think?  
  
EDIT: it would probably be a separate algorithm with separate strategies, cartesian based on area and non-cartesian based on azimuths/exterior angles. These strategies would be passed into `correct()` instead of area strategy and internally into this new algorithm.

---

## Comment 9

> Username: vissarion  
> Created_at: 2019-02-08 14:35:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461822090  

@awulkiew for your example the area algorithm first computes the azimuths (included the series method of https://github.com/boostorg/geometry/pull/500)  
```  
    andoyer:  0.7368231425  
    thomas:   0.7368276537  
    vincenty: 0.7368276516  
    series:   0.7368276523  
```  
then the spherical terms computed (using azimuths). Actually, spherical terms are areas of the trapezoid   
```  
POLYGON((lon1 lat1, lon2 lat2, lon2 0, lon1 0, lon1 lat1))  
```  
where `SEGMENT(lon1 lat1, lon2 lat2)` a segment of the triangle. So the area algorithm is summing up the (signed) areas of those trapezoids.   
  
These are the values for our example above (first 3 columns are terms for each one of the segments of the triangle and the last is the sum).   
```  
andoyer:  -3.24251737939E-008	-1.00518974731E-005	1.00842838828E-005  -3.87640939008E-011  
thomas:   -3.24255415970E-008	-1.00518882518E-005	1.00843420644E-005   2.82710029991E-011  
vincenty: -3.24255001954E-008	-1.00518793811E-005	1.00843435151E-005   3.86338046024E-011  
series:   -3.24255415035E-008	-1.00518882233E-005	1.00843420361E-005   2.82712965011E-011  
```  
  
That is, for small segments the difference in the areas are small and then an error in the 6-th decimal digit in the azimuth computation for andoyer leads to an error in the 10-th digit for the spherical term but the sum is of order $10^{-11}$ that is why the result is unexpected.

---

## Comment 10

> Username: vissarion  
> Created_at: 2019-02-08 14:38:26 UTC  
> Updated_at: 2019-02-11 07:38:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-461823076  

@awulkiew regarding ordering and area I also think using area is an overkill and what you suggest sounds great! For cartesian both ordering and area computations rely on determinants (actually the first only needs the sign so there is also space for optimizations here) but for spherical and geographic things are totally different so what you say is very natural.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2019-02-11 20:58:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-462491538  

I could not review this weekend, now planned for Wednesday

---

## Comment 12

> Username: awulkiew  
> Created_at: 2019-02-11 22:11:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-462515638  

No need to hurry. Before merging this PR I'd like to propose another PR: point-order check not based on area in non-cartesian coordinate systems. So this would be a separate algorithm (probably detail for now) with separate strategies which could be used here but also in correct, read_wkt, set operations and buffer.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2019-03-01 21:26:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-468817187  

I've implemented an algorithm which solves the issue above: https://github.com/boostorg/geometry/pull/561

---

## Comment 14

> Username: awulkiew  
> Created_at: 2019-03-06 01:15:06 UTC  
> Updated_at: 2019-03-10 19:08:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-469925716  

Rings order is now checked and interior rings assigned to exterior rings. I added IO strategies for this. These are umbrella strategies without `apply()` function, returning point-order and within(Pt, Ring) strategies. Later I plan to add the third: disjoint(Pt, Pt) strategy and use umbrella IO strategies in `read_wkt`.  
  
This PR now depends on: https://github.com/boostorg/geometry/pull/561

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2019-04-04 18:18:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/554#issuecomment-480008275  

Started review - compilation errors and warnings marked in the review (easy to fix), thanks for the example, easy to implement and runs fine. Will continue later.

---
