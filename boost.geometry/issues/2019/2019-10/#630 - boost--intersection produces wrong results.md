# #630 - boost::intersection produces wrong results [Closed]

> Username: innova-engineering  
> Created at: 2019-10-25 10:48:13 UTC  
> Updated at: 2022-06-08 08:44:04 UTC  
> Closed at: 2022-06-08 08:44:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/630  

This is related to #548   
Consider the following:  
```  
#include <boost/test/unit_test.hpp>  
#include <boost/geometry.hpp>  
  
  
void testIntersection(const std::string& polyString, const std::string& multiPolyString)  
{  
    using namespace boost::geometry;  
    using point2d = model::d2::point_xy<double>;  
    using polygon = model::polygon<point2d, true, false>;  
    using multiPolygon = model::multi_polygon<polygon>;  
  
    polygon poly;  
    multiPolygon multiPoly;  
    multiPolygon result;  
  
    read_wkt(polyString, poly);  
    read_wkt(multiPolyString, multiPoly);  
  
    intersection(multiPoly, poly, result);  
  
    BOOST_CHECK_GE(area(poly), area(result));  
    BOOST_CHECK_EQUAL(!result.empty(), intersects(multiPoly, poly));  
}  
  
BOOST_AUTO_TEST_CASE(Test1)  
{  
    testIntersection("POLYGON((-0.3 -0.1475,-0.3 +0.1475,+0.3 +0.1475,+0.3 -0.1475,-0.3 -0.1475))",   
                    "MULTIPOLYGON(((-0.605 +0.1575,+0.254777333596 +1.0172773336,+1.53436796127 -0.262313294074,+0.674590627671 -1.12209062767,-0.605 +0.1575)))");  
}  
  
BOOST_AUTO_TEST_CASE(Test2)  
{  
    testIntersection("POLYGON((-0.3 -0.1475,-0.3 +0.1475,+0.3 +0.1475,+0.3 -0.1475,-0.3 -0.1475))",   
                    "MULTIPOLYGON(((-1.215 +0.7675000000000001,-0.4962799075873666 +1.486220092412633,+0.665763075292561 +0.324177109532706,-0.05295701712007228 -0.3945429828799273,-1.215 +0.7675000000000001)))");  
}  
  
BOOST_AUTO_TEST_CASE(Test3)  
{  
    testIntersection("POLYGON((-0.3 -0.1475,-0.3 +0.1475,+0.3 +0.1475,+0.3 -0.1475,-0.3 -0.1475))",   
                    "MULTIPOLYGON(((-0.9099999999999999 +0.4625,-0.1912799075873667 +1.181220092412633,+0.9707630752925609 +0.01917710953270602,+0.2520429828799277 -0.6995429828799273,-0.9099999999999999 +0.4625)))");  
}  
```  
  
All data represent polygons that actually intersect.   
  
Running the tests with **BOOST_GEOMETRY_NO_ROBUSTNESS** not defined results in:  
Test1: check !result.empty() == intersects(multiPoly, poly) has failed [false != true]  
Test2: passed  
Test3: check !result.empty() == intersects(multiPoly, poly) has failed [false != true]  
  
Running the tests with **BOOST_GEOMETRY_NO_ROBUSTNESS** defined results in:  
Test1: passed  
Test2: check area(poly) >= area(result) has failed [0.17699999999999999 < 1.6093370446956148]  
Test3: check !result.empty() == intersects(multiPoly, poly) has failed [false != true]  
  
So defining **BOOST_GEOMETRY_NO_ROBUSTNESS** as proposed in #548 do not fix all results and introduces new bugs.  
  
Boost version: 1.70 and 1.71  
Compiler: MSVC 19.16.27032.1  
System: Windows 10

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-11-06 10:51:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/630#issuecomment-550256444  

I can reproduce this.  
I'm working on a solution - but this will not make it in 1.72  
I hope it will be fixed in 1.73

---

## Comment 2

> Username: mloskot  
> Created at: 2021-02-15 08:30:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/630#issuecomment-779050421  

Moving this to milestone 1.76

---

## Comment 3

> Username: tinko92  
> Created at: 2022-05-29 21:00:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/630#issuecomment-1140523577  

I can no longer reproduce this with 1.79 (!empty and intersects are consistent and first area is always greater or equal compared to the second), so it might be fixed.

---

## Comment 4

> Username: vissarion  
> Created at: 2022-06-07 14:07:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/630#issuecomment-1148725846  

On my setting (gcc-9, 20.04.1-Ubuntu, x86_64) I still find inconsistent results between `!result.empty()` and `intersects(multiPoly, poly)` both with 1.79 and develop.

---

## Comment 5

> Username: tinko92  
> Created at: 2022-06-07 15:17:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/630#issuecomment-1148814039  

For reference this is the code I used to test it:  
  
```cpp  
#include <boost/geometry.hpp>  
  
  
void testIntersection(const std::string& polyString, const std::string& multiPolyString)  
{  
    using namespace boost::geometry;  
    using point2d = model::d2::point_xy<double>;  
    using polygon = model::polygon<point2d, true, false>;  
    using multiPolygon = model::multi_polygon<polygon>;  
  
    polygon poly;  
    multiPolygon multiPoly;  
    multiPolygon result;  
  
    read_wkt(polyString, poly);  
    read_wkt(multiPolyString, multiPoly);  
  
    intersection(multiPoly, poly, result);  
  
    std::cout << area(poly) << " GE " << area(result) << "\n";  
    std::cout << !result.empty() << " == " << intersects(multiPoly, poly) << "\n";  
}  
  
int main()  
{  
    testIntersection("POLYGON((-0.3 -0.1475,-0.3 +0.1475,+0.3 +0.1475,+0.3 -0.1475,-0.3 -0.1475))",   
                    "MULTIPOLYGON(((-0.605 +0.1575,+0.254777333596 +1.0172773336,+1.53436796127 -0.262313294074,+0.674590627671 -1.12209062767,-0.605 +0.1575)))");  
    testIntersection("POLYGON((-0.3 -0.1475,-0.3 +0.1475,+0.3 +0.1475,+0.3 -0.1475,-0.3 -0.1475))",   
                    "MULTIPOLYGON(((-1.215 +0.7675000000000001,-0.4962799075873666 +1.486220092412633,+0.665763075292561 +0.324177109532706,-0.05295701712007228 -0.3945429828799273,-1.215 +0.7675000000000001)))");  
    testIntersection("POLYGON((-0.3 -0.1475,-0.3 +0.1475,+0.3 +0.1475,+0.3 -0.1475,-0.3 -0.1475))",   
                    "MULTIPOLYGON(((-0.9099999999999999 +0.4625,-0.1912799075873667 +1.181220092412633,+0.9707630752925609 +0.01917710953270602,+0.2520429828799277 -0.6995429828799273,-0.9099999999999999 +0.4625)))");  
    return 0;  
}  
```  
(I'm not familiar with boost unit tests).  
  
And this is my output:  
```  
0.177 GE 0.177  
1 == 1  
0.177 GE 0.171391  
1 == 1  
0.177 GE 0.177  
1 == 1  
```  
  
With GCC 12.1 and Clang 13.0.1, ArchLinux and current develop (commit 2ee0967344121c71a7e95bae84bf821e98a32cf3).

---

## Comment 6

> Username: vissarion  
> Created at: 2022-06-08 08:44:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/630#issuecomment-1149634345  

In unit tests rescaling was enabled by default in some cases in set operations, that is why it was failing. I propose to remove the switching to rescaling in those tests in https://github.com/boostorg/geometry/pull/1015.  
In any case this issue is now fixed (without rescaling which is the default).
