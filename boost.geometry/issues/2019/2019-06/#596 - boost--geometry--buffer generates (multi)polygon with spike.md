# #596 - boost::geometry::buffer generates (multi)polygon with spike [Closed]

> Username: meastp  
> Created at: 2019-06-20 13:41:48 UTC  
> Updated at: 2019-06-28 08:35:31 UTC  
> Closed at: 2019-06-28 08:35:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/596  

The following test case generates a (multi)polygon with a spike in Boost.Geometry v 1.70  
  
Changing from `boost::geometry::strategy::buffer::join_miter` to `boost::geometry::strategy::buffer::join_round` resolves the issue (no spike). However, as I think no spikes should be produced, I created this issue.  
  
```  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
#include <boost/geometry/io/svg/write_svg.hpp>  
#include <iostream>  
#include <fstream>  
  
int main()  
{  
	typedef boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian> point_t;  
	typedef boost::geometry::model::linestring<point_t> linestring_t;  
	linestring_t ls;  
  
	ls.push_back(point_t{ 292979.660, 6688731.370 });  
	ls.push_back(point_t{ 292979.600, 6688733.420 });  
	ls.push_back(point_t{ 292979.540, 6688735.440 });  
	ls.push_back(point_t{ 292979.540, 6688735.500 });  
  
	typedef boost::geometry::model::polygon<point_t> polygon_t;  
	typedef boost::geometry::model::multi_polygon<polygon_t> mpolygon_t;  
	mpolygon_t _buffer1;  
  
	auto dTol = 0.015;  
	const int nNumP_Circle = 12;  
	boost::geometry::strategy::buffer::distance_symmetric<double> distance_strategy(dTol);  
	boost::geometry::strategy::buffer::join_miter join_strategy;  
	boost::geometry::strategy::buffer::end_round end_strategy(nNumP_Circle);  
	boost::geometry::strategy::buffer::side_straight side_strategy;  
	boost::geometry::strategy::buffer::point_circle point_strategy;  
  
	boost::geometry::buffer(ls, _buffer1, distance_strategy, side_strategy, join_strategy, end_strategy, point_strategy);  
  
	std::ofstream svg("outfile.svg");  
  
	std::ostringstream out;  
	out << std::fixed << std::setprecision(6) << boost::geometry::wkt(_buffer1);  
  
	auto str = out.str();  
}  
```  
  
str is:  
```  
MULTIPOLYGON(((292979.585007 6688733.419555,292979.585007 6688733.419555,292979.525007 6688735.439555,292979.524018 6688735.513277,292979.525000 6688735.440000,292979.525000 6688735.500000,292979.527010 6688735.507500,292979.532500 6688735.512990,292979.540000 6688735.515000,292979.547500 6688735.512990,292979.552990 6688735.507500,292979.555000 6688735.500000,292979.555000 6688735.440229,292979.614993 6688733.420445,292979.611924 6688733.494046,292979.614994 6688733.420439,292979.674994 6688731.370439,292979.673204 6688731.362883,292979.667877 6688731.357235,292979.660439 6688731.355006,292979.652883 6688731.356796,292979.647235 6688731.362123,292979.645006 6688731.369561,292979.585007 6688733.419555)))  
```  
![spike](https://user-images.githubusercontent.com/1156416/59853584-711fae80-9371-11e9-9e50-40ae4354d06f.PNG)

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-06-26 10:48:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/596#issuecomment-505823602  

Thanks for the report.  
I cannot solve this right now, but I added it to the test suite and will keep the ticket open.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-06-26 12:24:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/596#issuecomment-505852001  

Hi Mats  
  
I added the testcase and could reproduce it (result is invalid). I looked a bit further and one of the internal calculations returned a wrong value. It is now replaced by another calculation (which was planned anyway, and related to the changes I am currently working on). So it is fixed now.  
  
Nice to hear from you again, by the way!  
  
Ciao, Barend

---

## Comment 3

> Username: barendgehrels  
> Created at: 2019-06-28 08:35:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/596#issuecomment-506652873  

It is fixed and merged. Thanks again for the report. It will be in 1.71
