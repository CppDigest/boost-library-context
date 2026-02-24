# #586 - boost::geometry::intersection produces wrong result for segments intersection point [Closed]

> Username: wylecho  
> Created at: 2019-04-17 09:44:26 UTC  
> Updated at: 2021-06-12 09:53:44 UTC  
> Closed at: 2021-06-12 09:04:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/586  

Hi everyone, I found a bug with the **boost::geometry::intersection()** function when computing the intersection point of two segments.   
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/foreach.hpp>  
#include <vector>  
#include <iostream>  
  
typedef boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> Point;  
typedef boost::geometry::model::segment<Point> Segment;  
  
int main()  
{  
    **const Segment segment0(Point(53770, -93790), Point(53770, 6410));  
    const Segment segment1(Point(105180, -92980), Point(5180, -92980));  
    std::vector<Point> intersect_points;  
    boost::geometry::intersection(segment0, segment1, intersect_points);**  
    for (size_t vi = 0; vi < intersect_points.size(); ++ vi)  
    {  
        std::cout << "intersection point #" << (int)vi << ": ("   
             << boost::geometry::get<0>(intersect_points[vi]) << ", " << boost::geometry::get<1>(intersect_points[vi]) << ")" << std::endl;  
    }  
    return 0;  
}  
  
![boost_bug](https://user-images.githubusercontent.com/13873608/56278022-087d4100-6138-11e9-8b0e-5026fc920b70.png)  
  
I tried version 1.53 and 1.63 and both of them has this issue. I am not sure whether it has been fixed now. I will have a try later. Thanks!

---

## Comment 1

> Username: wylecho  
> Created at: 2019-04-18 06:43:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/586#issuecomment-484376657  

**Update:**  
The latest version 1.70 also has this issue:  
![geometry_gub](https://user-images.githubusercontent.com/13873608/56341637-4b96ed00-61e8-11e9-817f-39b4c1904d59.png)

---

## Comment 2

> Username: ayushgupta138  
> Created at: 2021-05-14 17:58:50 UTC  
> Updated at: 2021-05-14 18:00:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/586#issuecomment-841409049  

Hi @wylecho   
I tried to reproduce the error mentioned above and ran the following code snippet,  
```cpp  
bg::model::segment<bg::model::point<double,2,bg::cs::cartesian>> s1, s2;  
	bg::read_wkt("SEGMENT(53770 -93790,53770 6410)", s1);  
	bg::read_wkt("SEGMENT(105180 -92980,5180 -92980)", s2);  
	vector<point2d> ans;  
	bg::intersection(s1, s2, ans);  
	for (int i = 0; i < ans.size(); i++)  
		cout << wkt(ans[i]) << "\n";  
```  
The above code snippet outputs,  
`POINT(53770 -92980)`  
which is the expected output according to   
![cap](https://user-images.githubusercontent.com/55524855/118309788-6218eb00-b50b-11eb-85d5-d9aa1c50b1c5.JPG)  
  
Environment used - Visual Studio 2019 on Windows 10 using Boost 1.76.0  
This should not be compiler-dependent in my view since no precision issues are involved, and no compiler-specific features are used.  
The issue is resolved in Boost.Geometry 1.76.0

---

## Comment 3

> Username: wylecho  
> Created at: 2021-06-12 09:04:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/586#issuecomment-860024935  

@ayushgupta138 Thanks for your reply. I also had a re-try and found it should be fixed now. Close this issue.

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-06-12 09:53:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/586#issuecomment-860030373  

In the original example `int` was used as coordinate type and in the reply `double`. So the code snippets are not the same. For `int` coordinate type various things can happen, e.g. overflow somewhere in the depths of the `intersection()`.
