# #662 - Incorrect result of area calculated for polygon [Closed]

> Username: linshu77  
> Created at: 2020-01-30 06:14:26 UTC  
> Updated at: 2020-02-03 17:20:34 UTC  
> Closed at: 2020-02-03 03:29:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/662  

Here is my code：	  
  
```  
typedef bg::model::d2::point_xy<double> DPoint;  
typedef bg::model::polygon<DPoint, false> DPolygon;  
typedef bg::model::multi_polygon<DPolygon> MPolygon;  
DPolygon poly1;  
DPolygon poly2;  
MPolygon poly;  
auto lstOf = boost::assign::list_of  
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
```  
  
The "Area" should be 0, but the result is 4.5. Why?It should be a mistake.  
  
-----  
  
@linshu77 Please, learn how to format GitHub issues. I also corrected the issue subject.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2020-01-30 19:06:47 UTC  
> Updated at: 2020-01-30 19:08:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-580406059  

Can you please try to compile and run the code with the define `BOOST_GEOMETRY_NO_ROBUSTNESS`?  
This will be the default in one of the future versions.

---

## Comment 2

> Username: linshu77  
> Created at: 2020-01-31 03:05:05 UTC  
> Updated at: 2020-01-31 22:36:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-580561469  

This is my code，It can be compiled by DEV-C + + 5.11.  
This incorrect result is caused by the function bg::intersection()  
  
```cpp  
#include <iostream>  
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

## Comment 3

> Username: barendgehrels  
> Created at: 2020-01-31 22:31:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-580939879  

Thanks for the code - but that was actually not my question...

---

## Comment 4

> Username: mloskot  
> Created at: 2020-01-31 22:36:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-580941524  

@linshu77 Please, once again, check https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax to learn how to format code blocks

---

## Comment 5

> Username: linshu77  
> Created at: 2020-02-01 03:20:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-580985810  

@mloskot Sorry, this is my first issue.

---

## Comment 6

> Username: linshu77  
> Created at: 2020-02-02 03:11:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-581093496  

When I change the data order, I will get the right result. why?  
  
```  
#include <iostream>  
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
	    (DPoint(171.60480351137778, 170.00000000000000))  
	    (DPoint(171.60480351137778, 162.32096650635697))  
	    (DPoint(172.21795926332567, 162.99736073096460))  
	    (DPoint(172.21795926332567, 170.00000000000000))  
	    (DPoint(171.60480351137778, 170.00000000000000));  
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

> Username: mloskot  
> Created at: 2020-02-02 19:09:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-581166432  

@linshu77 Can you run the test that @barendgehrels requested in his earlier comment?  
  
Simply, add the `#define BOOST_GEOMETRY_NO_ROBUSTNESS` at the top of your `.cpp` file like so  
  
```cpp  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
#include <iostream>  
#include <boost/assign.hpp>  
#include <boost/assign/list_of.hpp>  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
using namespace std;  
namespace bg = boost::geometry;  
int main()  
{  
...  
}  
```  
  
build and run it.

---

## Comment 8

> Username: linshu77  
> Created at: 2020-02-03 03:29:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-581221622  

Thank you，add the ` #define BOOST_GEOMETRY_NO_ROBUSTNESS` , It will get the right results.

---

## Comment 9

> Username: barendgehrels  
> Created at: 2020-02-03 17:20:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/662#issuecomment-581522308  

@linshu77 : Thanks for testing and good to hear it solves the problem.  
@mloskot  : Thanks for your assistence
