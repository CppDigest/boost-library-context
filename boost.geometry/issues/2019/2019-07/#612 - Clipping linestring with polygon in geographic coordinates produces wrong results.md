# #612 - Clipping linestring with polygon in geographic coordinates produces wrong results [Closed]

> Username: gruuk  
> Created at: 2019-07-16 19:57:15 UTC  
> Updated at: 2019-09-27 12:49:55 UTC  
> Closed at: 2019-09-27 12:49:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/612  

The following code:  
```cpp  
    namespace bg = boost::geometry;  
    using point_t = bg::model::point<double, 2, bg::cs::geographic<bg::radian>>;  
    using polygon_t = bg::model::polygon<point_t>;  
    using line_t = bg::model::linestring<point_t>;  
    using multiline_t = bg::model::multi_linestring<line_t>;  
  
    polygon_t tile;  
    bg::read_wkt(  
        "POLYGON((-0.392699 1.57e-07,-0.392699 0.19635,0 0.19635,0 1.57e-07,-0.392699 1.57e-07))",  
        tile);  
  
    line_t line;  
    bg::read_wkt("LINESTRING(-0.0872665 0.0872665, -0.0872665 -0.0872665)", line);  
  
    multiline_t clipped;  
    bg::strategy::intersection::geographic_segments<> strategy;  
    bg::intersection(tile, line, clipped, strategy);  
  
    std::cout << bg::wkt(clipped) << std::endl;  
```  
creates clipped linestring:  
```  
MULTILINESTRING((-0.0872665 0.0872665,-0.120524 1.59339e-07))  
```  
while it should be:  
```  
MULTILINESTRING((-0.0872665 0.0872665,-0.0872665 1.59339e-07))  
```  
  
![boos_intersection_error](https://user-images.githubusercontent.com/52977033/61324799-89b9a200-a813-11e9-8b89-07025855d12f.png)

---

## Comment 1

> Username: mark-grimes  
> Created at: 2019-09-03 13:18:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-527454350  

I have a similar problem when using `difference()`  
```C++  
namespace bg=boost::geometry;  
using point_type=bg::model::point<double,2,bg::cs::spherical_equatorial<bg::degree>>;  
using linestring_type=bg::model::linestring<point_type>;  
using polygon_type=bg::model::polygon<point_type>;  
  
const polygon_type polygon{{  {-106.374074, 39.638593}, {-106.373626, 39.639230}, {-106.373594, 39.639232},  
    {-106.373366, 39.638502}, {-106.373299, 39.638459}, {-106.373369, 39.638382}, {-106.374074, 39.638593 } }};  
const linestring_type line{ { -106.373725, 39.638846 }, { -106.373486, 39.639362 }, { -106.368378, 39.614603 } };  
std::vector<linestring_type> results;  
  
std::cout << "The polygon " << (bg::is_valid(polygon) ? "is" : "is NOT") << " valid"  
    << ", the line " << (bg::is_valid(line) ? "is" : "is NOT") << " valid\n";  
  
bg::difference( line, polygon, results );  
  
std::cout << "There are " << results.size() << " results\n";  
for( const auto& resultLine : results )  
{  
    std::cout << bg::wkt(resultLine) << "\n";  
}  
```  
Output:  
```  
The polygon is valid, the line is valid  
There are 1 results  
LINESTRING(-106.374 39.6392,-106.373 39.6385,-106.373 39.6385)  
```  
Expected:  
```  
The polygon is valid, the line is valid  
There are 2 results  
LINESTRING(-106.373575 39.639170,-106.373486 39.639362,-106.373300 39.638459)  
LINESTRING(-106.373300 39.638458,-106.368378 39.614603)  
```  
  
The following 3 images are the geometries at increasing zoom.  The black line is the input and the red is the output.  Notice that the input line just strays inside the polygon again at the eastern most point, hence why I would expect 2 line string results.  
<img width="838" alt="BoostDifferenceBug" src="https://user-images.githubusercontent.com/6480160/64176037-81193b80-ce54-11e9-8108-ee2df35fc005.png">  
  
I could put getting one result down to numerical error (given how close the cross over is), but the result makes no sense even if the line doesn't re-enter the polygon.

---

## Comment 2

> Username: mark-grimes  
> Created at: 2019-09-03 13:36:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-527461584  

I should have said that is with boost v1.70.0

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-09-03 17:36:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-527561596  

It seems I missed this issue. Sorry about that. I'll have a look.

---

## Comment 4

> Username: awulkiew  
> Created at: 2019-09-03 17:37:42 UTC  
> Updated at: 2019-09-03 17:38:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-527562067  

Btw, I can confirm that this issue (both) exists in develop branch so also certainly in 1.71.

---

## Comment 5

> Username: awulkiew  
> Created at: 2019-09-03 18:04:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-527572221  

The original issue (reported by @gruuk) is caused by the wrong result calculated by `bg::formula::sjoberg_intersection` inside `bg::strategy::intersection::geographic_segments`, independent of the direct/inverse formula used.  
  
The cause of the second issue (reported by @mark-grimes) is different. The intersection points (or turns) are calculated correctly (WRT error caused by the fact that one of the linestring's segments is very long) so the error must be in the subsequent part of the `difference` algorithm.

---

## Comment 6

> Username: vissarion  
> Created at: 2019-09-04 08:02:30 UTC  
> Updated at: 2019-09-04 08:39:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-527790274  

@gruuk @awulkiew it seems there is an issue with division by zero in https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/sjoberg_intersection.hpp#L852   
Also (at least in my system) replacing `double` with `long double` in the code above return the correct results.  EDIT: in develop branch

---

## Comment 7

> Username: gruuk  
> Created at: 2019-09-04 09:32:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-527823107  

@vissarion if this is the cause, then the same operations are done in https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/sjoberg_intersection.hpp#L837  
Could have similar consequences in some use cases.

---

## Comment 8

> Username: mark-grimes  
> Created at: 2019-09-05 15:35:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-528428006  

Since my bug is not the same, shall I log it as a new issue?  
  
I can confirm that the intersection points (turns(?)) are correct.  I don't understand the code but I get the impression the loop at [geometry/algorithms/detail/overlay/follow.hpp#L455](https://github.com/boostorg/geometry/blob/c7ba195a9e6ab39833c1500909d192a63332ae6c/include/boost/geometry/algorithms/detail/overlay/follow.hpp#L455) expects the intersection points to be in order of distance along the line string, which they're not. So it looks (to my untrained eye) that the sort at [geometry/algorithms/detail/overlay/follow.hpp#L446](https://github.com/boostorg/geometry/blob/c7ba195a9e6ab39833c1500909d192a63332ae6c/include/boost/geometry/algorithms/detail/overlay/follow.hpp#L446) doesn't work as expected.

---

## Comment 9

> Username: vissarion  
> Created at: 2019-09-06 12:11:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-528830006  

@mark-grimes yes please open a new issue

---

## Comment 10

> Username: awulkiew  
> Created at: 2019-09-20 14:28:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-533576953  

@gruuk I just merged a fix. Could you confirm that the issue is fixed by: https://github.com/boostorg/geometry/pull/620 ?

---

## Comment 11

> Username: gruuk  
> Created at: 2019-09-27 12:49:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/612#issuecomment-535925279  

The project where I wanted to use this functionality is completed. I can't test the solution in a wider context. However, the test case provided by me works and I'm closing the issue.
