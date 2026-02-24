# #893 - Multipolygon difference computes wrong result [Closed]

> Username: linmma  
> Created at: 2021-07-30 08:03:30 UTC  
> Updated at: 2024-11-25 13:11:37 UTC  
> Closed at: 2024-11-25 13:11:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/893  

I compute intersection and difference of multipolygons p and q. The intersection is not empty but the difference is empty. This seems to be wrong.  
  
Here is the code (also on godbolt https://godbolt.org/z/11fK95Wxj)  
  
```cpp  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
  
#include <boost/geometry.hpp>  
#include <iostream>  
#include <string>  
  
namespace bg = boost::geometry;  
  
using Point = boost::geometry::model::d2::point_xy<double>;  
using Polygon = boost::geometry::model::polygon<Point>;  
using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
  
struct Info {  
  double area;  
  bool isValid;  
  std::string failureMsg;  
  std::string name;  
};  
  
Info computeInfo(const MultiPolygon& multipolygon, const std::string& name) {  
  std::string failureMsg;  
  const auto isValid = bg::is_valid(multipolygon, failureMsg);  
  const auto area = bg::area(multipolygon);  
  
  return Info{area, isValid, failureMsg, name};  
}  
  
std::ostream& operator<<(std::ostream& os, const Info& info) {  
  os << info.name << '\n';  
  os << "area: " << info.area << '\n';  
  os << "isValid: " << std::boolalpha << info.isValid << '\n';  
  os << "failureMsg: " << info.failureMsg << '\n';  
  
  return os;  
}  
  
int main() {  
  MultiPolygon p{{{  
      {-9133.3885344331684, 3976.3162451998137},  
      {-6748.2449169873034, -5735.0734557728138},  
      {12359.886942916415, -1042.0645095456412},  
      {5126.3084924076147, 2226.9708710750697},  
      {-1604.5619839035633, 573.85084904357439},  
      {-9133.3885344331684, 3976.3162451998137},  
  }}};  
  
  MultiPolygon q{{{  
      {-3228.4265340177531, 1307.7159344890201},  
      {-4500.2645033380131, 1882.4913860267370},  
      {-4294.7752070657516, 1045.8178117890784},  
      {-3228.4265340177531, 1307.7159344890201},  
  }}};  
  
  bg::correct(p);  
  std::cout << computeInfo(p, "p") << '\n';  
  
  bg::correct(q);  
  std::cout << computeInfo(q, "q") << '\n';  
  
  std::cout << "p overlaps q: " << std::boolalpha << bg::overlaps(p, q) << '\n';  
  std::cout << "q whithin p: " << std::boolalpha << bg::within(q, p) << '\n';  
  std::cout << "p touches q: " << std::boolalpha << bg::touches(p, q) << '\n';  
  std::cout << "q touches p: " << std::boolalpha << bg::touches(q, p) << '\n';  
  // std::cout << "relation: " << bg::relation(q, p).str() << '\n';  
  std::cout << '\n';  
  
  MultiPolygon intersection;  
  
  bg::intersection(p, q, intersection);  
  std::cout << computeInfo(intersection, "intersection") << '\n';  
  
  MultiPolygon difference;  
  
  bg::difference(p, q, difference);  
  std::cout << computeInfo(difference, "difference") << '\n';  
}  
```  
  
Output:  
```  
p  
area: 9.76869e+07  
isValid: true  
failureMsg: Geometry is valid  
  
q  
area: 473002  
isValid: true  
failureMsg: Geometry is valid  
  
p overlaps q: true  
q whithin p: false  
p touches q: false  
q touches p: false  
  
intersection  
area: 473002  
isValid: true  
failureMsg: Geometry is valid  
  
difference  
area: 0  
isValid: true  
failureMsg: Geometry is valid  
```  
  
![multipolgons-p-q-intersection-difference](https://user-images.githubusercontent.com/87026039/127618082-324ba755-aa2b-4881-aff6-bfef62ddbc62.png)  
  
On the image it seems p is overlapping q and that they are touching. Maybe this is problematic. How should one handle those cases?  
  
From the image it seems to be clear that the difference should be not empty.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-11-23 13:52:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/893#issuecomment-1325097657  

Sorry for the late feedback. Yes, I can reproduce it and it is erroneous.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2022-11-23 13:56:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/893#issuecomment-1325103281  

It's a numerical issue (obviously). With `float` or `long double`, this specific test is fine, at my side.

---

## Comment 3

> Username: linmma  
> Created at: 2022-11-23 14:30:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/893#issuecomment-1325157977  

Hi Barend,  
  
for me it is quite challenging to use boost geometry for polygon processing in production correctly. I don't really know how to be sure about getting correct results.   
  
Currently I do this:  
- Rescale the coordinates using knowledge in my environment.  
- Detect bad points and filter them myself.  
- Use isValid function to detect bad input.  
- Use correct function to correct bad input.  
- Use buffer for deflating and inflating to correct bad input.  
- For difference and intersection computation I compute the areas, sum add them up and test if the sum fits.  
  
This is al lot of effort and wastes a lot of performance. It would be good to know if there are better alternatives to use boost geometry for polygon processing. Of course, using a type with more precision would help. But other than that?  
  
Best regards  
Marcel

---

## Comment 4

> Username: barendgehrels  
> Created at: 2022-11-30 11:45:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/893#issuecomment-1332024641  

Hi Marcel,  
  
Thanks for your feedback. That indeed looks like inconvenient workarounds.  
  
However, I don't know the current state of other libraries. I did some benchmarks before, but that is long ago and was mainly w.r.t. performance.  
  
Yes, using a `boost::multiprecision` is what I would suggest.  
  
That solves the problem for me in your case. But it will be a bit slower.  
  
  
Here my adapted code  
```  
#include <boost/geometry.hpp>  
#include <iostream>  
#include <string>  
  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
namespace bg = boost::geometry;  
  
template <typename G>  
void computeInfo(const std::string& title, const G& geometry)  
{  
	std::cout << std::boolalpha;  
	std::cout << title << ": " << boost::geometry::area(geometry) << " " << boost::geometry::is_valid(geometry) << std::endl;  
}  
  
template <typename T>  
void Test()  
{  
	using Point = boost::geometry::model::d2::point_xy<T>;  
	using Polygon = boost::geometry::model::polygon<Point>;  
	using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
	MultiPolygon p{ {{  
			{T(-9133.3885344331684), T(3976.3162451998137)},  
			{T(-6748.2449169873034), T(-5735.0734557728138)},  
			{T(12359.886942916415), T(-1042.0645095456412)},  
			{T(5126.3084924076147), T(2226.9708710750697)},  
			{T(-1604.5619839035633), T(573.85084904357439)},  
			{T(-9133.3885344331684), T(3976.3162451998137)},  
	}} };  
  
	MultiPolygon q{ {{  
					{T(-3228.4265340177531), T(1307.7159344890201)},  
					{T(-4500.2645033380131), T(1882.4913860267370)},  
					{T(-4294.7752070657516), T(1045.8178117890784)},  
					{T(-3228.4265340177531), T(1307.7159344890201)},  
	}} };  
  
	bg::correct(p);  
	computeInfo("p", p);  
  
	bg::correct(q);  
	computeInfo("q", q);  
  
  
	MultiPolygon intersection;  
  
	bg::intersection(p, q, intersection);  
	computeInfo("intersection", intersection);  
  
	MultiPolygon difference;  
  
	bg::difference(p, q, difference);  
	computeInfo("difference", difference);  
}  
  
int main()   
{  
	Test<float>();  
	Test<double>();  
	Test<long double>();  
	Test<boost::multiprecision::number<boost::multiprecision::cpp_bin_float<5>>>();  
  
	return 0;  
}  
```

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-11-16 10:32:47 UTC  
> Updated at: 2024-11-16 10:37:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/893#issuecomment-2480509289  

Not completely fixed on latest branch, or in PR #1343   
  
* `float`: correct  
* `double:` difference `b-a` incorrect  
* `boost::multiprecision::cpp_bin_float<50>>`: correct

---

## Comment 6

> Username: barendgehrels  
> Created at: 2024-11-24 12:03:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/893#issuecomment-2495965765  

Fixed, PR in review
