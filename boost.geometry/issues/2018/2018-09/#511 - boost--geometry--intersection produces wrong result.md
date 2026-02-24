# #511 - boost::geometry::intersection produces wrong result [Open]

> Username: olologin  
> Created at: 2018-09-25 20:29:28 UTC  
> Updated at: 2019-01-09 18:38:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/511  

Hi everyone again, looks like I've spotted another problem, and it doesn't look similar to https://github.com/boostorg/geometry/issues/510 so I'm creating another issue.  
  
Also, this problem is reproducible in boost 1.55 and in latest dev branch. Didn't test any other branches.  
Code to reproduce:  
  
```  
typedef d2::point_xy<double>				BoostPoint;  
typedef polygon<BoostPoint >				BoostPolygon;  
typedef linestring<BoostPoint>				BoostPolyline;  
  
int main(void)  
{  
    BoostPolyline polyToClip;  
    std::vector<BoostPolyline> clippingResult;  
  
    polyToClip.push_back(BoostPoint(-21.855920181274413, -0.94793533329159096));  
    polyToClip.push_back(BoostPoint(-15.353896496297601, -0.94793533329159096));  
    polyToClip.push_back(BoostPoint(-15.144153796782220, -0.94793533329159096));  
    polyToClip.push_back(BoostPoint(-9.0384241485595709, -0.94793533329159096));  
  
  
    multi_polygon<BoostPolygon> clippingRegion;  
    BoostPolygon curPolygon;  
    append(curPolygon, BoostPoint(-15.280303647629022, -0.94793533329159185));  
    append(curPolygon, BoostPoint(-14.5, -1));  
    append(curPolygon, BoostPoint(-14.157121780656512, -0.94840134008253141));  
    append(curPolygon, BoostPoint(-14.157452756077651, -0.94726404792587238));  
    append(curPolygon, BoostPoint(-14.5, -0.9));  
    append(curPolygon, BoostPoint(-15.280303647629022, -0.94793533329159185));  
    clippingRegion.push_back(curPolygon);  
    reverse(clippingRegion);  
  
    intersection(clippingRegion, polyToClip, clippingResult);  
    return 0;  
}  
```  
  
Result is not correct because it leaves whole part of polyToClip from left side of clippingRegion.  
![image](https://user-images.githubusercontent.com/4762581/46041127-c7312f80-c11a-11e8-85d8-034d3f7e06b6.png)

---

## Comment 1

> Username: olologin  
> Created at: 2018-09-26 10:14:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/511#issuecomment-424661850  

Same issue, but with vertical segment:  
```  
typedef d2::point_xy<double>				BoostPoint;  
typedef polygon<BoostPoint >				BoostPolygon;  
typedef linestring<BoostPoint>				BoostPolyline;  
  
int main(void)  
{  
    BoostPolyline polyToClip;  
    std::vector<BoostPolyline> clippingResult;  
  
    polyToClip.push_back(BoostPoint(-1.1355107225663756, 44.811363220214844));  
    polyToClip.push_back(BoostPoint(-1.1355107225663756, 31.354686837217578));  
    polyToClip.push_back(BoostPoint(-1.1355107225663756, 30.054206349090208));  
    polyToClip.push_back(BoostPoint(-1.1355107225663756, 29.991532590626239));  
	polyToClip.push_back(BoostPoint(-1.1355107225663756, 22.658702850341797));  
  
  
    multi_polygon<BoostPolygon> clippingRegion;  
    BoostPolygon curPolygon;  
    append(curPolygon, BoostPoint(-1.7983527943465329, 30.743617692193869));  
    append(curPolygon, BoostPoint(-1.1355107225663748, 30.040013195475236));  
    append(curPolygon, BoostPoint(-1.1301423455046056, 31.385884317235718));  
    append(curPolygon, BoostPoint(-1.1694242244004023, 31.381292355386186));  
    append(curPolygon, BoostPoint(-1.7983527943465329, 30.743617692193869));  
     
    clippingRegion.push_back(curPolygon);  
    reverse(clippingRegion);  
  
    intersection(clippingRegion, polyToClip, clippingResult);  
    return 0;  
}  
```

---

## Comment 2

> Username: MikeAirey  
> Created at: 2019-01-09 11:27:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/511#issuecomment-452664183  

Here is another example of `intersection` not returning the expected results, demonstrating that it may be an issue with very small floating point differences. This sample code finds the intersection between the same box and circle twice, first with a 1000-point circle polygon and then with a 100-point circle polygon. The higher resolution intersection fails, and the lower resolution intersection succeeds.  
  
```  
#include <iostream>  
#include "boost/geometry/geometry.hpp"  
  
using namespace boost::geometry;  
typedef model::d2::point_xy<double> Pt;  
typedef model::polygon<Pt> P;  
typedef std::vector<P> PV;  
typedef model::box<Pt> Box2D;  
  
void TestIntersection(double radius, double x, double y, int resolution)  
{  
    const double pi = boost::math::constants::pi<double>();  
    const double increment = 2 * pi / resolution;  
    std::vector<Pt> coords;  
    double angle = 0;  
    for (int i = 0; i < resolution; ++i, angle += increment)  
    {  
        coords.push_back(Pt(x + (radius * cos(angle)), y + (radius * sin(angle))));  
    }  
    coords.push_back(coords[0]);  
    P theCircle;  
    assign_points(theCircle, coords);  
    correct(theCircle);  
  
    Box2D theBox(Pt(0, 0), Pt(x, y));  
    correct(theBox);  
  
    PV intersectionPolygons;  
    intersection(theBox, theCircle, intersectionPolygons);  
  
    std::cout << "Box: " << std::endl << dsv(theBox) << std::endl;  
    std::cout << "Circle: " << std::endl << dsv(theCircle) << std::endl;  
    if (intersectionPolygons.size() > 0)  
    {  
        std::cout << "Intersection of box and circle: " << std::endl << dsv(intersectionPolygons[0]) << std::endl;  
    }  
    else  
    {  
        std::cout << "Intersection of box and circle: none" << std::endl;  
    }  
    std::cout << std::endl;  
    std::cout << std::endl;  
}  
  
int main()  
{  
    TestIntersection(1, 1, 1, 1000);  
    TestIntersection(1, 1, 1, 100);  
}  
```  
  
Results:  
1000 points:  
![image](https://user-images.githubusercontent.com/7135657/50896588-8eb12980-145d-11e9-8a63-1534bfdd37d8.png)  
![image](https://user-images.githubusercontent.com/7135657/50896614-a2f52680-145d-11e9-8d6b-26b36c6451cf.png)  
Intersection  
![image](https://user-images.githubusercontent.com/7135657/50896634-b7392380-145d-11e9-83f0-ae27ad31ea88.png)  
  
100 points:  
![image](https://user-images.githubusercontent.com/7135657/50896529-5b6e9a80-145d-11e9-86a9-810e006b3a66.png)  
![image](https://user-images.githubusercontent.com/7135657/50896542-65909900-145d-11e9-8138-91b3fa68a917.png)  
Intersection  
![image](https://user-images.githubusercontent.com/7135657/50896570-79d49600-145d-11e9-8e66-e01bdbe52d0e.png)

---

## Comment 3

> Username: barendgehrels  
> Created at: 2019-01-09 12:49:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/511#issuecomment-452685057  

Hi @olologin , thank you - I'm busy with it (removing rescaling internally) but this takes quite a while. The enhancement will be released one of coming releases (but probably not yet next release).  
I will use your tests for verification.  
Regards, Barend

---

## Comment 4

> Username: olologin  
> Created at: 2019-01-09 18:38:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/511#issuecomment-452807867  

Hi @barendgehrels , Thank you too. It's cool that someone is going to take care of it. Thank you for attention to this issue.
