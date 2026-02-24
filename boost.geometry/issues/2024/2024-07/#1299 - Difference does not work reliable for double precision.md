# #1299 - Difference does not work reliable for double precision [Closed]

> Username: MauriceHubain  
> Created at: 2024-07-29 14:03:33 UTC  
> Updated at: 2024-11-25 13:11:38 UTC  
> Closed at: 2024-11-25 13:11:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1299  

This is a follow up ticket for  https://github.com/boostorg/geometry/issues/1288.  
  
Adapting your test code from that ticket with a new data set, we get wrong results for double and long double precision:  
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <iostream>  
#include <iomanip>  
  
namespace bg = boost::geometry;  
  
template <typename T>  
void test_Difference_B83673_1_MP()  
{  
  using point_t = bg::model::point<T, 2, bg::cs::cartesian>;  
  using polygon_t = bg::model::polygon<point_t, false, false>;  
  using mp_t = bg::model::multi_polygon<polygon_t>;  
  
  mp_t poly1;  
  bg::read_wkt("MULTIPOLYGON(((1.2549999979079400 0.85000000411847698, -1.2550000020920500 0.84999999897038103, -1.2549999999999999 -0.85000000102961903, 1.2549999999999999 -0.84999999999999998)))", poly1);  
  bg::correct(poly1);  
  
  mp_t poly2;  
  bg::read_wkt("MULTIPOLYGON(((-0.87500000000000000 -0.84999999999999998, -0.87500000000000000 -0.070000000000000201, -1.2549999999999999 -0.070000000000000201, -1.2549999999999999 -0.84999999999999998)))", poly2);  
  bg::correct(poly2);  
  
  mp_t diff, inters;  
  bg::difference(poly1, poly2, diff);  
  bg::intersection(poly1, poly2, inters);  
  
  std::cout << std::setprecision(12) << std::boolalpha;  
  std::cout  
    << "Type: " << typeid(T).name()  
    << " a: " << bg::area(poly1)  
    << " b: " << bg::area(poly2)  
    << " diff: " << bg::area(diff)  
    << " ints: " << bg::area(inters)  
    << " a-b: " << bg::area(poly1) - bg::area(poly2)  
    << " d+i " << bg::area(diff) + bg::area(inters)  
    << " validity diff: " << bg::is_valid(diff)  
    << " ints: " << bg::is_valid(inters)  
    << std::endl;  
}  
  
template <typename T>  
void test_Difference_B83673_1_P()  
{  
  using point_t = bg::model::point<T, 2, bg::cs::cartesian>;  
  using polygon_t = bg::model::polygon<point_t, false, false>;  
  using mp_t = bg::model::multi_polygon<polygon_t>;  
  
  mp_t poly1;  
  bg::read_wkt("MULTIPOLYGON(((1.2549999979079400 0.85000000411847698, -1.2550000020920500 0.84999999897038103, -1.2549999999999999 -0.85000000102961903, 1.2549999999999999 -0.84999999999999998)))", poly1);  
  bg::correct(poly1);  
  
  polygon_t poly2;  
  bg::read_wkt("POLYGON((-0.87500000000000000 -0.84999999999999998, -0.87500000000000000 -0.070000000000000201, -1.2549999999999999 -0.070000000000000201, -1.2549999999999999 -0.84999999999999998))", poly2);  
  bg::correct(poly2);  
  
  mp_t diff, inters;  
  bg::difference(poly1, poly2, diff);  
  bg::intersection(poly1, poly2, inters);  
  
  std::cout << std::setprecision(12) << std::boolalpha;  
  std::cout  
    << "Type: " << typeid(T).name()  
    << " a: " << bg::area(poly1)  
    << " b: " << bg::area(poly2)  
    << " diff: " << bg::area(diff)  
    << " ints: " << bg::area(inters)  
    << " a-b: " << bg::area(poly1) - bg::area(poly2)  
    << " d+i " << bg::area(diff) + bg::area(inters)  
    << " validity diff: " << bg::is_valid(diff)  
    << " ints: " << bg::is_valid(inters)  
    << std::endl;  
}  
  
int main()   
{  
  test_Difference_B83673_1_MP<float>();  
  test_Difference_B83673_1_MP<double>();  
  test_Difference_B83673_1_MP<long double>();  
  std::cout << std::endl;  
  
  test_Difference_B83673_1_P<float>();  
  test_Difference_B83673_1_P<double>();  
  test_Difference_B83673_1_P<long double>();  
  std::cout << std::endl;  
}  
  
```  
  
  
Output:  
Type: float a: 4.26700010347 b: 0.296400005227 diff: 3.97060009825 ints: 0.296400005227 a-b: 3.97060009825 d+i 4.26700010347 validity diff: true ints: true  
Type: double a: 4.26700000517 b: 0.2964 diff: 0 ints: 0.2964 a-b: 3.97060000517 d+i 0.2964 validity diff: true ints: true  
Type: long double a: 4.26700000517 b: 0.2964 diff: 0 ints: 0.2964 a-b: 3.97060000517 d+i 0.2964 validity diff: true ints: true  
  
Type: float a: 4.26700010347 b: 0.296400005227 diff: 3.97060009825 ints: 0.296400005227 a-b: 3.97060009825 d+i 4.26700010347 validity diff: true ints: true  
Type: double a: 4.26700000517 b: 0.2964 diff: 0 ints: 0.2964 a-b: 3.97060000517 d+i 0.2964 validity diff: true ints: true  
Type: long double a: 4.26700000517 b: 0.2964 diff: 0 ints: 0.2964 a-b: 3.97060000517 d+i 0.2964 validity diff: true ints: true

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-07-29 15:46:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1299#issuecomment-2256281301  

This is indeed also wrong and now is wrong for both multi and poly.  
Thanks for the report.  
It is not yet fixed by my concept fix for #1293 and #1294 and #1295

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-11-15 17:49:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1299#issuecomment-2479593339  

It is not fixed by my concept fix for #1226 and #1326

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-11-24 12:03:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/1299#issuecomment-2495965605  

Fixed, PR in review
