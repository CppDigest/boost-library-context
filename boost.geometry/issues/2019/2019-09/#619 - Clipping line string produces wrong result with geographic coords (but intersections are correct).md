# #619 - Clipping line string produces wrong result with geographic coords (but intersections are correct) [Closed]

> Username: mark-grimes  
> Created at: 2019-09-17 09:41:31 UTC  
> Updated at: 2019-11-06 16:27:53 UTC  
> Closed at: 2019-11-06 16:27:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/619  

The effect is very similar to #612 but discussion on that thread shows the underlying cause is different.  
  
(most of the below copied from #612)  
  
## Original problem  
Failing code:  
```c++  
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
  
The following 3 images are the geometries at increasing zoom. The black line is the input and the red is the output. Notice that the input line just strays inside the polygon again at the eastern most point, hence why I would expect 2 line string results.  
  
  
![](https://user-images.githubusercontent.com/6480160/64176037-81193b80-ce54-11e9-8108-ee2df35fc005.png)  
  
  
My test is with boost v1.70.0 but it has been reproduced with the head version (as at 03/Sep/2019)  
  
## Investigation  
  
Stepping through the code shows that the intersections are calculated correctly.  It looks like the loop at [geometry/algorithms/detail/overlay/follow.hpp#L455](https://github.com/boostorg/geometry/blob/c7ba195a9e6ab39833c1500909d192a63332ae6c/include/boost/geometry/algorithms/detail/overlay/follow.hpp#L455) expects the intersection points to be in order of distance along the line string, which they're not. So it looks (to my untrained eye) that the sort at [geometry/algorithms/detail/overlay/follow.hpp#L446](https://github.com/boostorg/geometry/blob/c7ba195a9e6ab39833c1500909d192a63332ae6c/include/boost/geometry/algorithms/detail/overlay/follow.hpp#L446) doesn't work as expected.

---

## Comment 1

> Username: mloskot  
> Created at: 2019-09-17 09:58:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/619#issuecomment-532151587  

I just want to say, what an excellent bug report!

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-09-19 23:01:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/619#issuecomment-533340273  

Thanks for creating separate bug report. I'll look into this.

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-11-06 16:27:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/619#issuecomment-550388737  

Thanks! This should be fixed now.
