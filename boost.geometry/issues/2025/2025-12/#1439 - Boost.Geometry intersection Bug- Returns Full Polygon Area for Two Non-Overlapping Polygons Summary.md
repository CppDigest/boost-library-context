# #1439 - Boost.Geometry intersection Bug: Returns Full Polygon Area for Two Non-Overlapping Polygons Summary [Open]

> Username: llpan91  
> Created at: 2025-12-02 07:54:39 UTC  
> Updated at: 2025-12-14 10:14:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1439  

When computing the intersection of two non-overlapping simple polygons,  
boost::geometry::intersection incorrectly returns a non-empty result, containing the full area of one polygon.  
  
This is a critical topology error: a false-positive intersection.  
  
Environment  
  
Boost version: 1.79 / 1.81 / 1.82 (bug reproduced across versions)  
  
Geometry models:  
  
boost::geometry::model::d2::point_xy<double>  
  
boost::geometry::model::polygon<Point>  
  
Compilers: GCC / Clang (bug reproduced consistently)  
  
Operating System: Linux / Ubuntu 22.04  
  
  
\\ data  
  
    BgPolygon p1 = createPolygon({  
        BgBgPoint(-2.47089026, -86.03059246),  
  
        BgBgPoint(-1.161944873, 146.3030596),  
        BgBgPoint(3.40221214, 145.8628015),  
        BgBgPoint(3.024695769, 141.949088),  
        BgBgPoint(2.420586453, 111.9554564),  
  
        BgBgPoint(1.208013978, -86.04069936),  
        BgBgPoint(-2.47089026, -86.03059246)  
    });  
  
  
  
    BgPolygon p2 = createPolygon({  
        BgPoint(-6.213278056, -88.01851748),  
        BgPoint(-6.207382255, -86.51852906),  
        BgPoint(-6.20539951, -86.01408604),  
        BgPoint(-2.470873099, -86.02434575),  
        BgPoint(-2.472221358, -86.53315586),  
        BgPoint(-2.476196085, -88.03315059),  
        BgPoint(-6.213278056, -88.01851748)  
    });

---

## Comment 1

> Username: barendgehrels  
> Created at: 2025-12-07 08:30:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/1439#issuecomment-3621816907  

Thanks for the report. Versions until `1.82` are quite old, per `1.88` is the intersection algorithm quite improved.  
  
Could you try it with version `1.89` please? Thanks!

---

## Comment 2

> Username: kashish2710  
> Created at: 2025-12-13 11:04:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1439#issuecomment-3649218290  

I tested this issue with Boost 1.89 in a Colab environment:  
  
- Boost version: 1.89 (confirmed via `boost/version.hpp`)  
- Environment: Google Colab Linux (GCC)  
- Result: `Intersection count: 1` (false-positive)  
- Minimal reproducible C++ file: https://gist.github.com/kashish2710/eba1ca881525296d6c964739921be4d7  
  
Screenshot from Colab output attached showing the result.  
  
This demonstrates that the false-positive intersection still occurs in Boost 1.89.  
  
I am willing to help by:  
1. Adding a proper unit test for this scenario in the Boost.Geometry test suite.  
2. Preparing a draft pull request with a fix in the future.  
  
<img width="1255" height="642" alt="Image" src="https://github.com/user-attachments/assets/4cde2fbf-2255-4475-9d78-79abc4ead726" />

---

## Comment 3

> Username: barendgehrels  
> Created at: 2025-12-14 10:13:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1439#issuecomment-3650660586  

Hi @kashish2710 thanks for testing this, and thanks for your both PR's (to which I commented both).  
  
OK so it still happens in `1.89` - I will have a closer look later this month.  
  
You offer your help for a concept fix, that will not be easy, but you are of course welcome to contribute! Getting the PR (with the unit test right) is a good first step.
