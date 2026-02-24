# #1212 - Incorrect results of boost::geometry::difference (using mpq_rational) [Open]

> Username: lxily  
> Created at: 2023-10-30 14:25:22 UTC  
> Updated at: 2024-11-15 22:19:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/1212  

Hi~, under version 1.83 (same as 1.81), I seem to have encountered an operation result error of boost::geometry::difference(r1, r2, res). The following is the corresponding polygon data:  
  
r1 = POLYGON((850000 0,1330000 320000,1350000 500000,1180000 1280000,790000 1280000,290000 1270000,220000 1190000,160000 1120000,30000 770000,20000 620000,0 110000,850000 0),(510711 675561,620711 945561,680711 735561,510711 675561),(1035911 461661,835911 421661,715911 361661,725911 381661,795911 411661,655911 501661,655911 541661,715911 591661,755911 691661,815911 821661,695911 761661,705911 781661,775911 811661,635911 901661,635911 941661,695911 991661,735911 1091661,795911 1221661,656523 1221661,526523 1241661,846523 1221661,925911 1221661,1025911 1201661,855911 1071661,735911 1031661,779571 1009831,915911 941661,1055911 971661,1065911 761661,1045911 801661,875911 671661,755911 631661,935911 541661,1075911 571661,1085911 361661,1035911 461661));  
r2 = POLYGON((711861 626536,741861 656536,651861 836536,711861 626536))  
  
Among them, polygon r1 is a polygon containing two holes, and r2 is a simple polygon, as shown in the following figure:  
![error](https://github.com/boostorg/geometry/assets/53161611/175d9a1e-0a25-4010-9752-640816c71815)  
  
The operation directly caused one of the holes to disappear, as shown in the figure below:  
![error_res](https://github.com/boostorg/geometry/assets/53161611/6fb7cdbc-fcf9-4059-920c-678e9dc42456)  
  
It is worth mentioning that the coordinates of all vertices of these two polygons are integers, and I use boost::multiprecision::mpq_rational as my basic data type, so in theory, there is no calculation precision error.  
  
The possible problem is that the polygon r2 is in touch with two holes of polygon r1 simultaneously, forming a complex polygon Boolean operation that leads to wrong results.  
  
Could you reproduce this situation?

---

## Comment 1

> Username: vissarion  
> Created at: 2023-12-05 10:27:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1212#issuecomment-1840465423  

Thanks for the data and for opening the issue. Could you please share a minimal piece of C++ code to reproduce the above situation?

---

## Comment 2

> Username: lxily  
> Created at: 2023-12-05 11:53:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1212#issuecomment-1840645428  

> Thanks for the data and for opening the issue. Could you please share a minimal piece of C++ code to reproduce the above situation?  
  
Sure, you can probably reproduce the problem and visualize it using the following code.  
  
```  
#include <iostream>  
#include <fstream>  
  
#include <boost/multiprecision/gmp.hpp>  
#include <boost/multiprecision/number.hpp>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/linestring.hpp>  
#include <boost/geometry/geometries/register/point.hpp>  
  
namespace bg = boost::geometry;  
namespace bm = boost::multiprecision;  
using Rational = bm::mpq_rational;  
  
struct RationalPoint {  
	Rational x_, y_;  
	RationalPoint() :x_(0), y_(0) {}  
	RationalPoint(Rational _x, Rational _y) : x_(_x), y_(_y) { }  
	const Rational x() const { return x_; }  
	const Rational y() const { return y_; }  
};  
  
BOOST_GEOMETRY_REGISTER_POINT_2D(RationalPoint, Rational, cs::cartesian, x_, y_)  
  
typedef bg::model::polygon<RationalPoint, false, true> RationalPolygon;  
typedef bg::model::multi_polygon<RationalPolygon> MRationalPolygon;  
  
void draw_polygon(const std::string & filename, const MRationalPolygon & polygons);  
  
int main() {  
	RationalPolygon pA, pB;  
	bg::read_wkt("POLYGON((850000 0,1330000 320000,1350000 500000,1180000 1280000,790000 1280000,290000 1270000,220000 1190000,160000 1120000,30000 770000,20000 620000,0 110000,850000 0),(510711 675561,620711 945561,680711 735561,510711 675561),(1035911 461661,835911 421661,715911 361661,725911 381661,795911 411661,655911 501661,655911 541661,715911 591661,755911 691661,815911 821661,695911 761661,705911 781661,775911 811661,635911 901661,635911 941661,695911 991661,735911 1091661,795911 1221661,656523 1221661,526523 1241661,846523 1221661,925911 1221661,1025911 1201661,855911 1071661,735911 1031661,779571 1009831,915911 941661,1055911 971661,1065911 761661,1045911 801661,875911 671661,755911 631661,935911 541661,1075911 571661,1085911 361661,1035911 461661))", pA);  
	bg::read_wkt("POLYGON((711861 626536,741861 656536,651861 836536,711861 626536))", pB);  
  
	MRationalPolygon res;  
	bg::difference(pA, pB, res);  
  
	std::cout << bg::wkt(res) << std::endl;  
  
	draw_polygon("pA-pB.svg", { pA, pB });  
	draw_polygon("result.svg", res);  
  
        return 0;  
}  
  
void draw_polygon(const std::string & filename, const MRationalPolygon & polygons) {  
	typedef bg::model::d2::point_xy<long double> pointf_t;  
	typedef bg::model::polygon<pointf_t, false, true> polygonf_t;  
	typedef polygonf_t::ring_type ringf_t;  
	typedef bg::model::multi_polygon<polygonf_t> mpolygonf_t;  
  
	auto to_long_double = [&](const Rational &val) {  
		return bm::numerator(val).convert_to<long double>() / bm::denominator(val).convert_to<long double>();  
	};  
  
	auto convert_to_polygonf_t = [&](const RationalPolygon & polygon) {  
		auto convert_to_ringf_t = [&](const RationalPolygon::ring_type &ring) {  
			ringf_t rf;  
			for (const RationalPoint & pt : ring) {  
				rf.push_back(pointf_t( to_long_double(pt.x()), to_long_double(pt.y()) ));  
			}  
			return rf;  
		};  
  
		polygonf_t pf;  
		pf.outer() = convert_to_ringf_t(polygon.outer());  
		for (const auto& r : polygon.inners()) { pf.inners().push_back(convert_to_ringf_t(r)); }  
  
		return pf;  
	};  
  
	mpolygonf_t mps;  
	for (const RationalPolygon &p : polygons) {  
		mps.emplace_back(convert_to_polygonf_t(p));  
	}  
  
	std::ofstream svg(filename.c_str());  
	bg::svg_mapper<pointf_t> mapper(svg, 200, 200, "width=\"400mm\" height=\"400mm\" viewBox=\"-100 -100 500 500\" style=\"background-color:white\"");  
	mapper.add(mps);  
	mapper.map(mps, "fill-opacity:0.5;fill:rgb(30,105,206);stroke:rgb(0,0,0);stroke-width:0.25");  
}  
```  
  
The version of Boost I currently use is 1.81.0, compiled with Visual Studio 2017. Thanks for your reply and efforts!

---

## Comment 3

> Username: lxily  
> Created at: 2023-12-05 12:20:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1212#issuecomment-1840688144  

You can also use the following data to generate polygon pA for simplification.  
  
```  
bg::read_wkt("POLYGON((850000 0,1350000 500000,790000 1280000,30000 770000,850000 0),(510711 675561,620711 945561,680711 735561,510711 675561),(1035911 461661,715911 591661,755911 691661,1035911 461661))", pA);  
```

---

## Comment 4

> Username: vissarion  
> Created at: 2023-12-05 14:39:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1212#issuecomment-1840924886  

Thanks @lxily I can reproduce your issue. Indeed the issue seems to be combinatorial and not numerical.  
  
Also, it seems that the issue is related to the fact that pB and the hole of pA touch on a common segment. If they only touch on a common point e.g. by using the following pB  
  
`bg::read_wkt("POLYGON((711861 626536,741861 656536,680711 735561,711861 626536))", pB);`  
  
then the result seems correct.

---

## Comment 5

> Username: lxily  
> Created at: 2023-12-05 14:59:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1212#issuecomment-1840966016  

> Thanks @lxily I can reproduce your issue. Indeed the issue seems to be combinatorial and not numerical.  
>   
> Also, it seems that the issue is related to the fact that pB and the hole of pA touch on a common segment. If they only touch on a common point e.g. by using the following pB  
>   
> `bg::read_wkt("POLYGON((711861 626536,741861 656536,680711 735561,711861 626536))", pB);`  
>   
> then the result seems correct.  
  
That's true, but I'm wondering if the difference() operations in the geometry library should support this situation/combinatorial. Actually, I can get the correct results using the CGAL.

---

## Comment 6

> Username: vissarion  
> Created at: 2023-12-05 16:57:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1212#issuecomment-1841227722  

Yes, it should.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2024-11-15 22:18:58 UTC  
> Updated at: 2024-11-15 22:19:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/1212#issuecomment-2480035663  

Just checked it on my side (verifying some other new fixes).  
  
* For me, it goes completely fine using `long` or `double`.  
* I don't have `gmp.h` so that does not work for me.  
* Using `bm::number<bm::cpp_dec_float<50>>` instead gives indeed problems  
* but it fails (now, for me) in `union_` and not in `difference`...  
  
Correct runs (for double - I cannot use bm in these visualizations yet)  
  
Union:  
![Image](https://github.com/user-attachments/assets/11451214-3c4d-4338-889e-291f981fd02e)  
  
Difference (green polygon (B) correctly subtracted):  
![Image](https://github.com/user-attachments/assets/c5ef4d76-6113-4193-b467-c18fa3927593)
