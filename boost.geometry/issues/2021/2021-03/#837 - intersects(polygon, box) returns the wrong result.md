# #837 - intersects(polygon, box) returns the wrong result [Closed]

> Username: vschoech  
> Created at: 2021-03-29 07:46:18 UTC  
> Updated at: 2022-05-03 17:11:37 UTC  
> Closed at: 2022-04-13 10:23:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/837  

This problem has been reported earlier and it **was fixed in 1.50** already:  
https://lists.boost.org/geometry/2012/06/1957.php  
  
It has now returned in boost.geometry 1.75.  
  
My `tc::geo::polygon` type is actually a multi-polygon, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.75.0**. Please consider the following example:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((1992 3240,1992 1440,3792 1800,3792 3240,1992 3240)))", polygonA); // does not throw  
boost::geometry::is_valid(polygonA); // returns true  
  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((1941 2066,2055 2066,2055 2166,1941 2166,1941 2066)))", polygonB); // does not throw  
boost::geometry::is_valid(polygonB); // returns true  
  
bool const bPolyPoly = boost::geometry::intersects(polygonA, polygonB); // returns true (correct)  
  
// the same shape as polygonB, but type box/rect  
tc::geo::rect<int> const rectB = Box(1941;2066;2055;2166); // pseudo code; not using WKT here b/c WKT represents boxes as polygons which is confusing in this context   
  
bool const bPolyBox = boost::geometry::intersects(polygonA, rectB); // returns false (WRONG)  
```

---

## Comment 1

> Username: vschoech  
> Created at: 2021-04-01 07:48:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/837#issuecomment-811718906  

P.S. Please add my reproduction as a unit test to your project to ensure that it doesn't break again! :)

---

## Comment 2

> Username: ayushgupta138  
> Created at: 2021-04-19 07:30:08 UTC  
> Updated at: 2021-04-19 07:37:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/837#issuecomment-822241164  

I can reproduce the error mentioned above.   
After some code reading, I found that the function `boost::geometry::all_segments_of()`   
https://github.com/boostorg/geometry/blob/e098a85f5adb5bbf28422a6415c6bf5ad601840a/include/boost/geometry/algorithms/detail/disjoint/areal_areal.hpp#L115  
is not performing in the desired way.  
https://github.com/boostorg/geometry/blob/e098a85f5adb5bbf28422a6415c6bf5ad601840a/include/boost/geometry/algorithms/for_each.hpp#L275  
The function `fe_segment_range_with_closure<open>::apply()` invokes the functor once instead of invoking the functor for all possible segments in an open polygon.  
I think the addition of a return statement   
https://github.com/boostorg/geometry/blob/e098a85f5adb5bbf28422a6415c6bf5ad601840a/include/boost/geometry/algorithms/for_each.hpp#L280  
along with some additional code before, would resolve the issue.  
But before that, I would like that the code contributor for this function @awulkiew, @mloskot, @brunolalande, and @barendgehrels to confirm.

---

## Comment 3

> Username: togermer  
> Created at: 2021-04-28 09:35:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/837#issuecomment-828309341  

I also ran into this issue and found `fe_segment_range_with_closure<open>::apply()` to be incorrect. It first checks the inner segments of the polygon ring for intersections, but discards the results. This can be simply fixed:  
  
`        if (! fe_segment_range_with_closure<closed>::apply(range, f))   
        {  
            return false;  
        }  
`

---

## Comment 4

> Username: awulkiew  
> Created at: 2022-04-12 11:27:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/837#issuecomment-1096602277  

> P.S. Please add my reproduction as a unit test to your project to ensure that it doesn't break again! :)  
  
@vschoech this case was tested but only for closed ring. :)

---

## Comment 5

> Username: awulkiew  
> Created at: 2022-04-12 11:28:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/837#issuecomment-1096602838  

Thanks @ayushgupta138 for the investigation and @togermer for the fix!
