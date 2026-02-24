# #663 - Incorrect result of the function bg::intersection() [Open]

> Username: linshu77  
> Created at: 2020-02-04 11:36:13 UTC  
> Updated at: 2024-11-24 10:06:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/663  

This is my code with the define BOOST_GEOMETRY_NO_ROBUSTNESS，  
It can be compiled by DEV-C + + 5.11,Boost version: 1.72.  
The answer should be 0, but it's 27.  
However, if I delete the "#define BOOST_GEOMETRY_NO_ROBUSTNESS",  
the answer will be correct.  
  
```  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
#include<iostream>  
#include <boost/assign.hpp>  
#include <boost/assign/list_of.hpp>  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
using namespace std;  
namespace bg = boost::geometry;  
int main()  
{  
	typedef bg::model::d2::point_xy<double> DPoint;  
	typedef bg::model::polygon<DPoint, false> DPolygon;  
	typedef bg::model::multi_polygon<DPolygon> MPolygon;  
	DPolygon poly1;  
	DPolygon poly2;  
	MPolygon poly;  
	std::list<DPoint> lstOf = boost::assign::list_of  
		(DPoint(140.10975222826562, 135.99989776999999))  
		(DPoint(140.10975222826562, 126.97244753050222))  
		(DPoint(140.16255693232961, 135.99989776999999))  
		(DPoint(140.10975222826562, 135.99989776999999));  
	poly1.outer().assign(lstOf.begin(), lstOf.end());  
	lstOf = boost::assign::list_of  
		(DPoint(151.99995129999999, 123.99989777000000))  
		(DPoint(139.99994018999999, 126.99990054000000))  
		(DPoint(129.99995129999999, 124.99989777000000))  
		(DPoint(151.99995129999999, 123.99989777000000));  
	poly2.outer().assign(lstOf.begin(), lstOf.end());  
	bg::correct(poly1);  
	bg::correct(poly2);  
	bg::intersection(poly1, poly2, poly);  
	double Area = bg::area(poly);  
	cout<<Area;  
	return 1;  
}  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2020-02-04 11:49:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/663#issuecomment-581871445  

What compiler and what version of the compiler is this `DEV-C + + 5.11` thing?  
  
What compilation flags did you use, especially what optimisations?

---

## Comment 2

> Username: linshu77  
> Created at: 2020-02-04 11:58:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/663#issuecomment-581874643  

I also compiled with MSVC2012 Ver11.0.51106.01.  
what optimisations? I don't know.

---

## Comment 3

> Username: mloskot  
> Created at: 2020-02-04 12:17:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/663#issuecomment-581884652  

It's quite important to know at least if you do non-optimised debug build or you do optimised build (Release in Visual Studio speak).

---

## Comment 4

> Username: linshu77  
> Created at: 2020-02-04 12:31:29 UTC  
> Updated at: 2020-02-04 12:35:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/663#issuecomment-581889163  

DEV-C + + 5.11 : TDM-GCC 4.9.2 32-bit Release.  
MSVC2012: Release and Debug  
The answers are all the same. They're all Incorrect.  
However, if I delete the "#define BOOST_GEOMETRY_NO_ROBUSTNESS",  
the answer will be correct.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-02-04 12:44:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/663#issuecomment-581893329  

Thanks for checking it's not optimisation related.  
  
> if I delete the "#define BOOST_GEOMETRY_NO_ROBUSTNESS", the answer will be correct.  
  
For some/many cases, that is what you should do, because without that macro defined, the algorithms will perform rescaling to integer for more numerically robust solution (default behaviour).   
  
https://github.com/boostorg/geometry/blob/b930d335653063cb6f9c9641956b3cac5f80e86a/include/boost/geometry/core/config.hpp#L25-L29  
  
You may still need to define the macro in order to disable the rescaling for some/edge cases as you discovered in the previous issue you reported #662.  
  
I think this case can be closed.

---

## Comment 6

> Username: linshu77  
> Created at: 2020-02-04 13:02:05 UTC  
> Updated at: 2020-02-04 13:56:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/663#issuecomment-581899380  

I have two code.  
If I add the "#define BOOST_GEOMETRY_NO_ROBUSTNESS",   
the code1 will be incorrect, the code2 will be correct.  
but if I delete the "#define BOOST_GEOMETRY_NO_ROBUSTNESS",  
the code1 will be correct, the code2 will be incorrect.  
what can I do? thanks.  
  
code1  
```  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
#include<iostream>  
#include <boost/assign.hpp>  
#include <boost/assign/list_of.hpp>  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
using namespace std;  
namespace bg = boost::geometry;  
int main()  
{  
	typedef bg::model::d2::point_xy<double> DPoint;  
	typedef bg::model::polygon<DPoint, false> DPolygon;  
	typedef bg::model::multi_polygon<DPolygon> MPolygon;  
	DPolygon poly1;  
	DPolygon poly2;  
	MPolygon poly;  
	std::list<DPoint> lstOf = boost::assign::list_of  
		(DPoint(140.10975222826562, 135.99989776999999))  
		(DPoint(140.10975222826562, 126.97244753050222))  
		(DPoint(140.16255693232961, 135.99989776999999))  
		(DPoint(140.10975222826562, 135.99989776999999));  
	poly1.outer().assign(lstOf.begin(), lstOf.end());  
	lstOf = boost::assign::list_of  
		(DPoint(151.99995129999999, 123.99989777000000))  
		(DPoint(139.99994018999999, 126.99990054000000))  
		(DPoint(129.99995129999999, 124.99989777000000))  
		(DPoint(151.99995129999999, 123.99989777000000));  
	poly2.outer().assign(lstOf.begin(), lstOf.end());  
	bg::correct(poly1);  
	bg::correct(poly2);  
	bg::intersection(poly1, poly2, poly);  
	double Area = bg::area(poly);  
	cout<<Area;  
	return 1;  
}  
```  
  
code2:  
```  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
#include<iostream>  
#include <boost/assign.hpp>  
#include <boost/assign/list_of.hpp>  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
using namespace std;  
namespace bg = boost::geometry;  
int main()  
{  
	typedef bg::model::d2::point_xy<double> DPoint;  
	typedef bg::model::polygon<DPoint, false> DPolygon;  
	typedef bg::model::multi_polygon<DPolygon> MPolygon;  
	DPolygon poly1;  
	DPolygon poly2;  
	MPolygon poly;  
	std::list<DPoint> lstOf = boost::assign::list_of  
		(DPoint(171.60480351137778, 162.32096650635697))  
		(DPoint(172.21795926332567, 162.99736073096460))  
		(DPoint(172.21795926332567, 170.00000000000000))  
		(DPoint(171.60480351137778, 170.00000000000000))  
		(DPoint(171.60480351137778, 162.32096650635697));  
	poly1.outer().assign(lstOf.begin(), lstOf.end());  
	lstOf = boost::assign::list_of  
		(DPoint(200.00000000000000, 166.00002000000001))  
		(DPoint(184.00004999999999, 166.00002000000001))  
		(DPoint(180.00004999999999, 164.00002000000001))  
		(DPoint(169.99998889000000, 162.00000277999999))  
		(DPoint(182.00000000000000, 159.00000000000000))  
		(DPoint(200.00000000000000, 159.00000000000000))  
		(DPoint(200.00000000000000, 166.00002000000001));  
	poly2.outer().assign(lstOf.begin(), lstOf.end());  
	bg::correct(poly1);  
	bg::correct(poly2);  
	bg::intersection(poly1, poly2, poly);  
	double Area = bg::area(poly);  
	cout<<Area;  
	return 1;  
}  
```

---

## Comment 7

> Username: awulkiew  
> Created at: 2020-02-04 15:56:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/663#issuecomment-581977927  

The first case looks like that:  
  
![image](https://user-images.githubusercontent.com/1226951/73760921-59653100-476e-11ea-8f25-fbf12bd3759e.png)  
  
red - poly1  
green - poly2  
blue - poly (result)  
  
With `BOOST_GEOMETRY_NO_ROBUSTNESS` the result is equal to the poly2 (with additional point). Without the result is empty. Even if the result was non-empty the intersection should not be equal to poly2.  
  
  
    [0] {140.10975222826562, 126.97244753050222}  
    [1] {139.99994018999999, 126.99990054000000}  
    [2] {129.99995129999999, 124.99989777000000}  
    [3] {151.99995129999999, 123.99989777000000}  
    [4] {140.10975222826562, 126.97244753050222}  
  
The CW/CCW point order does not affect the result. Tested with VS2017.

---

## Comment 8

> Username: awulkiew  
> Created at: 2020-02-04 16:01:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/663#issuecomment-581980516  

The second case:  
  
![image](https://user-images.githubusercontent.com/1226951/73761780-9978e380-476f-11ea-9572-d4ed126e70de.png)  
  
As @linshu77 said it's the other way around WRT `BOOST_GEOMETRY_NO_ROBUSTNESS`. However this time the poly1 is the result.  
  
    {171.60480351137778, 162.32096650635697}  
    {172.21795926332567, 162.99736073096460}  
    {172.21795926332567, 170.00000000000000}  
    {171.60480351137778, 170.00000000000000}  
    {171.60480351137778, 162.32096650635697}
