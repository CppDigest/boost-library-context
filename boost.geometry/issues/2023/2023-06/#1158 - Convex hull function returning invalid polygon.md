# #1158 - Convex hull function returning invalid polygon [Closed]

> Username: andrea-cimatoribus-pix4d  
> Created at: 2023-06-01 15:00:38 UTC  
> Updated at: 2023-09-27 07:15:47 UTC  
> Closed at: 2023-09-27 07:15:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1158  

I discovered a behaviour that seems badly incorrect in `boost::geometry::convex_hull` function, in relation to collinear points. It's clearly an edge case, but I think it points to some underlying weakness of the algorithm, that I could not identify. The issue is summarised by the following image:  
![ch](https://github.com/boostorg/geometry/assets/121491611/1984c9ea-c059-4ff3-b1ac-e7c6c532301b)  
which is the minimal example I could extract from the original dataset that presented the issue.  
  
The full code to reproduce is the following (without the plots, a quick hack in Python):  
```cpp  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
#include <iostream>  
                                           
using Point = boost::geometry::model::d2::point_xy<double>;  
using Points = boost::geometry::model::multi_point<Point>;                                                                                                              
using BoostPolygon = boost::geometry::model::polygon<Point, true, false>;  
  
void check_hull(const Points& points)  
{  
    BoostPolygon hull;  
    boost::geometry::convex_hull(points, hull);  
  
    // just in case  
    boost::geometry::correct(hull);  
  
    std::cout << "\nTest case:\n";  
    for (const auto& p : points)  
        std::cout << "(" << p.x() << ", " << p.y() << ")\n";  
  
    std::cout << "\n";  
    if (boost::geometry::is_valid(hull))  
    {  
        std::cout << "Valid convex hull!\n";  
    }  
    else  
    {  
        std::cout << "Invalid convex hull:\n";  
        for (const auto& p : hull.outer())  
            std::cout << "(" << p.x() << ", " << p.y() << ")\n";  
    }  
}  
  
int main(int, char**)  
{  
    Points points;  
    boost::geometry::append(points, Point(1941.6475737576565734, 554.21197550796682663));  
    boost::geometry::append(points, Point(2201.1796067026721175, 604.83253590728611471));  
    boost::geometry::append(points, Point(2367.1836939680897558, 1898.3918136409306499));  
    boost::geometry::append(points, Point(1856.9044662310534477, 2320.8057089752910542));  
    // troublesome points  
    boost::geometry::append(points, Point(2000.0000000000002274, 551.77450949119793222));  
    boost::geometry::append(points, Point(1999.9999999999995453, 1721.4008337980080796));  
    boost::geometry::append(points, Point(1999.9999999999993179, 1966.6530570371892281));  
  
    check_hull(points);  
  
    return 0;  
}  
```  
  
When I compile with gcc `11.3.0`, running on Ubuntu 22, I get `Invalid convex hull` and the clearly incorrect output shown in the figure above. I could not check on other platforms. In the experiments I did, the use of all 3 collinear points listed in the code is necessary to reproduce the problem. On the other hand, the exact position of the four corners of the convex hull does not matter much (as long they remain the corners of the convex hull). Also increasing the y-coordinate of the lowest collinear point, so that it is not a vertex of the convex hull any more, does not avoid the problem (all three collinear points are still included as vertices of the convex hull).  
  
I digged a bit into the tests of the library, and I found some custom strategy definition whose name seemed promising:  
```cpp  
struct robust_cartesian : boost::geometry::strategies::convex_hull::cartesian<>  
{  
    static auto side()  
    {  
        return boost::geometry::strategy::side::side_robust  
            <  
                double,  
                boost::geometry::strategy::side::fp_equals_policy  
            >();  
    }  
};  
```  
however using this custom strategy in the test above did not change the results.  
  
Is this a bug or am I using the library somehow incorrectly?  
  
Might be related to https://github.com/boostorg/geometry/issues/829

---

## Comment 1

> Username: lopeLH  
> Created at: 2023-06-02 07:48:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1158#issuecomment-1573305539  

Also affected by this issue :(

---

## Comment 2

> Username: tinko92  
> Created at: 2023-06-02 11:12:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/1158#issuecomment-1573562805  

The example seems to work with rescaling/older versions of BG, bisect points to commmit 8774b7b2d7049ee9baf021204c772faa97de9642 , which disables rescaling. But, I'm surprised it doesn't work with the robust side strategy, I'll look into that.

---

## Comment 3

> Username: vissarion  
> Created at: 2023-06-02 11:42:57 UTC  
> Updated at: 2023-06-02 11:44:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/1158#issuecomment-1573600853  

@andrea-cimatoribus-pix4d thanks for filing this issue. @tinko92 thanks for the response.   
  
To my understanding the issue is not in the side strategy (i.e. the side predicate) and the "collinear" points but rather in the x-coordinates of those three (almost) collinear points that forces the [sorting preproccessing](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/convex_hull/graham_andrew.hpp#L210) of the convex hull algorithm to return incorrect sorting results.   
  
Note, that in my system your example run with points parameterized with `long double` coordinates returns correct (valid) results. Basically because the algorithm sorts the input with more precision yielding correct sorting results.   
  
Also note that if you call the side strategy for the three (almost) collinear points you will get a non-zero result (i.e. non-collinear points). This is tested with `side_robust`, `side_non_robust` and `side_by_triangle` with both `double` and `long double` coordinates.  
  
I am willing to propose a solution with a PR next week.

---

## Comment 4

> Username: andrea-cimatoribus-pix4d  
> Created at: 2023-06-05 06:19:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1158#issuecomment-1576114354  

Thanks for your replies. I checked on my machine and indeed using `long double` works. On the other hand, I must say I don't get the remark on the side strategy, but I think that's only my poor understanding of this code. Looking forward to a solution, and please let me know if I can contribute somehow.

---

## Comment 5

> Username: zowers  
> Created at: 2023-09-27 06:20:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1158#issuecomment-1736771794  

should the bug be closed as long as #1162 merged?
