# #1419 - Difference fails for double precision [Open]

> Username: MauriceHubain  
> Created at: 2025-07-23 14:51:18 UTC  
> Updated at: 2026-01-06 14:58:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1419  

Difference does not work properly with double precision for some corner cases:  
  
output1 = input0 - input1  
output2 = output1 - input2  
  
expected behavior and result with float precision: output1 ≈ output2  
result with double precision: output2 is empty  
  
```  
#include <boost/geometry.hpp>  
  
  
template <typename T>  
void test_Bug83673_3()  
{  
  using Point2d         = boost::geometry::model::d2::point_xy<T>;  
  using Polygon2d       = boost::geometry::model::polygon<Point2d, false, false>;  
  using MultiPolygon2d  = boost::geometry::model::multi_polygon<Polygon2d>;  
  
  MultiPolygon2d input0;  
  boost::geometry::read_wkt("MULTIPOLYGON(((1.25499999790794003 0.850000004118476982,-1.25500000209204998 0.849999998970381032,-1.25499999999999989 -0.850000001029619034,1.25499999999999989 -0.849999999999999978,1.25499999790794003 0.850000004118476982)))", input0);  
  boost::geometry::correct(input0);  
  
  Polygon2d input1;  
  boost::geometry::read_wkt("POLYGON((-0.875 -0.849999999999999978,-0.875 -0.070000000000000201,-1.25499999999999989 -0.070000000000000201,-1.25499999999999989 -0.849999999999999978,-0.875 -0.849999999999999978))", input1);  
  boost::geometry::correct(input1);  
  
  MultiPolygon2d output1;  
  boost::geometry::difference(input0, input1, output1);  
  
  
  Polygon2d input2;  
  boost::geometry::read_wkt("POLYGON((-1.25499999999999989 -0.850000001029619034,-1.25500000209204998 0.849999998970381032,-1.43000000230740998 1.02499999861144997,-1.42999999978463999 -1.02500000110140999,-1.25499999999999989 -0.850000001029619034))", input2);  
  boost::geometry::correct(input2);  
  
  MultiPolygon2d output2;  
  boost::geometry::difference(output1, input2, output2);  
  
  
  std::cout << "test_Bug83673_3" << "<" << typeid(T).name() << ">" << std::endl;  
  std::cout << "input0:" << boost::geometry::to_wkt(input0) << "\tarea:" << boost::geometry::area(input0) << std::endl;  
  std::cout << "input1:" << boost::geometry::to_wkt(input1) << "\tarea:" << boost::geometry::area(input1) << std::endl;  
  std::cout << "output1:" << boost::geometry::to_wkt(output1) << "\tarea:" << boost::geometry::area(output1) << std::endl;  
  
  std::cout << "input2:" << boost::geometry::to_wkt(input2) << "\tarea:" << boost::geometry::area(input2) << std::endl;  
  std::cout << "output2:" << boost::geometry::to_wkt(output2) << "\tarea:" << boost::geometry::area(output2) << std::endl;  
  std::cout << std::endl;  
}  
  
  
int main()  
{  
  test_Bug83673_3<float>();  
  test_Bug83673_3<double>();  
  
  return 0;  
}  
```  
  
Output:  
```  
test_Bug83673_3<float>  
input0:MULTIPOLYGON(((1.255 0.85,-1.255 0.85,-1.255 -0.85,1.255 -0.85,1.255 0.85)))     area:4.267  
input1:POLYGON((-0.875 -0.85,-0.875 -0.07,-1.255 -0.07,-1.255 -0.85,-0.875 -0.85))      area:0.2964  
output1:MULTIPOLYGON(((-0.875 -0.07,-0.875 -0.85,1.255 -0.85,1.255 0.85,-1.255 0.85,-1.255 -0.07,-0.875 -0.07)))       area:3.9706  
input2:POLYGON((-1.255 -0.85,-1.255 0.85,-1.43 1.025,-1.43 -1.025,-1.255 -0.85))        area:0.328125  
output2:MULTIPOLYGON(((-0.875 -0.07,-0.875 -0.85,1.255 -0.85,1.255 0.85,-1.255 0.85,-1.255 -0.07,-0.875 -0.07)))       area:3.9706  
  
test_Bug83673_3<double>  
input0:MULTIPOLYGON(((1.255 0.85,-1.255 0.85,-1.255 -0.85,1.255 -0.85,1.255 0.85)))     area:4.267  
input1:POLYGON((-0.875 -0.85,-0.875 -0.07,-1.255 -0.07,-1.255 -0.85,-0.875 -0.85))      area:0.2964  
output1:MULTIPOLYGON(((1.255 0.85,-1.255 0.85,-1.255 -0.85,1.255 -0.85,1.255 0.85),(-1.255 -0.07,-0.875 -0.07,-0.875 -0.85,-1.255 -0.85,-1.255 -0.07))) area:3.9706  
input2:POLYGON((-1.255 -0.85,-1.255 0.85,-1.43 1.025,-1.43 -1.025,-1.255 -0.85))        area:0.328125  
output2:MULTIPOLYGON()  area:0  
```  
  
This was tested with boost 1.88.  
With boost 1.71 we get the same (proper) results for float and double precision.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2025-07-23 18:00:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1419#issuecomment-3109589588  

Thanks for the report.  
Could you maybe also test with `1.89` (in preparation)? Things have changed a lot...

---

## Comment 2

> Username: MauriceHubain  
> Created at: 2025-07-24 08:55:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1419#issuecomment-3112631048  

I tested it on branch "boost-1.89.0.beta1" and got the same result.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2025-07-24 16:08:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1419#issuecomment-3114044724  

Thanks for testing!

---

## Comment 4

> Username: MauriceHubain  
> Created at: 2026-01-06 14:58:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1419#issuecomment-3715026001  

Re-tested with 1.90. The issue is still present.
