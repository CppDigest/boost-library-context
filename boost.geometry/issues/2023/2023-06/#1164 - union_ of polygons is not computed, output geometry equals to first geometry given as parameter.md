# #1164 - union_ of polygons is not computed, output geometry equals to first geometry given as parameter [Closed]

> Username: BenoitAndrieu  
> Created at: 2023-06-19 19:32:11 UTC  
> Updated at: 2023-09-14 09:39:42 UTC  
> Closed at: 2023-09-14 09:39:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/1164  

Hello, I have a case where the union of two polygons is not computed. The result is the first polygon given as input parameter of union_  
I am using boost 1.82, model is cartesian with **floats**  
Compiler is MSVC 17.6.2  
  
Link to godbolt in gcc [https://godbolt.org/z/bxWKKj8j5](https://godbolt.org/z/bxWKKj8j5)  
  
I have not tested with older versions.  
Thanks for any hints  
  
```  
TEST(TestCaseName, TestFusionPolygone)  
{  
	using boost_coordinate_t = float;  
	using boost_point_2d = boost::geometry::model::d2::point_xy<boost_coordinate_t>;  
	using boost_polygon_2d = boost::geometry::model::polygon<boost_point_2d>;  
	using boost_multipolygon_2d = boost::geometry::model::multi_polygon<boost_polygon_2d>;  
   
	auto big = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((14.47634792327880859375 -9.395435333251953125,3.7363479137420654296875 -9.395435333251953125,3.7163479328155517578125 -9.37543487548828125,3.5163478851318359375 -9.17543506622314453125,3.6163480281829833984375 -9.17543506622314453125,3.6163480281829833984375 -9.0554351806640625,14.13634777069091796875 -9.0554351806640625,14.13634777069091796875 -0.93543493747711181640625,2.3563480377197265625 -0.93543493747711181640625,2.286347866058349609375 -0.935434818267822265625,0.81634795665740966796875 -0.93543493747711181640625,0.6963479518890380859375 -0.815434932708740234375,0.4963479340076446533203125 -0.615434944629669189453125,0.4763479530811309814453125 -0.5954349040985107421875,14.47634792327880859375 -0.5954349040985107421875,14.47634792327880859375 -9.395435333251953125)))");  
	auto big_area = boost::geometry::area(big);  
   
	auto other = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((2.286347866058349609375 -4.42543506622314453125,2.286347866058349609375 -0.93543493747711181640625,2.3563480377197265625 -0.93543493747711181640625,2.3563480377197265625 -4.42543506622314453125,2.286347866058349609375 -4.42543506622314453125)))");  
	auto other_area = boost::geometry::area(other);  
   
	boost_multipolygon_2d union_result;  
	boost::geometry::union_(big, other, union_result);  
	auto union_area = boost::geometry::area(union_result);  
   
	EXPECT_EQ(union_result.size(), 1);  
	EXPECT_GT(union_area, big_area);  
   
	auto wkt_result = boost::geometry::to_wkt(union_result, std::numeric_limits<boost_coordinate_t>::digits10);  
}  
```  
![image](https://github.com/boostorg/geometry/assets/7237306/d41bd552-733a-4121-8633-f96c92da2d10)

---

## Comment 1

> Username: BenoitAndrieu  
> Created at: 2023-06-20 07:38:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1164#issuecomment-1598274453  

Hi,  
I tried to define BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE  
Using **floats**, it is a better, the output looks like the union is done, there are however two polygons instead of one.

---

## Comment 2

> Username: vissarion  
> Created at: 2023-06-20 08:23:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1164#issuecomment-1598336851  

Could you please also try with `double` with and without defining BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE?  
There are cases (see https://github.com/boostorg/geometry/issues/1145) where using `double` solved the issue.

---

## Comment 3

> Username: BenoitAndrieu  
> Created at: 2023-06-20 08:40:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1164#issuecomment-1598360990  

Yes, thanks for the hint, it indeed works with `double`, I had tried and did update the ticket but I cannot find it, maybe I did not submit the form...  
  
However I have more issues using `double` than with using `floats`, I could do some tickets about them ! :wink:

---

## Comment 4

> Username: vissarion  
> Created at: 2023-06-20 09:03:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1164#issuecomment-1598393776  

thanks @BenoitAndrieu for the report. Just one clarification, when you say it works with double, do you mean it works with `double` without defining BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE?

---

## Comment 5

> Username: BenoitAndrieu  
> Created at: 2023-06-20 10:40:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1164#issuecomment-1598532949  

With the code above using `double` and not defining BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE, the output does produce two polygons which is better than with `float`  
  
However, running my full app, with `double` the output is a single polygon which is the expected result.  
  
I extracted the WKT in `double` :  
```  
"MULTIPOLYGON(((0.476347946586042014782691467189579270780086517333984375 -0.5954349332325516996888836729340255260467529296875,14.4763479465860402939370032981969416141510009765625 -0.5954349332325516996888836729340255260467529296875,14.47634794658603851758016389794647693634033203125 -9.3954349332325488575179406325332820415496826171875,3.7363479465860418571310219704173505306243896484375 -9.3954349332325488575179406325332820415496826171875,3.5163479465860429939993991865776479244232177734375 -9.1754349332325499943863178486935794353485107421875,3.616347946586042638728031306527554988861083984375 -9.1754349332325499943863178486935794353485107421875,3.616347946586042638728031306527554988861083984375 -9.055434933232550775983327184803783893585205078125,8.3863479465860422124023898504674434661865234375 -9.055434933232550775983327184803783893585205078125,11.2863479465860425676737577305175364017486572265625 -9.0554349332325525523401665850542485713958740234375,14.1363479465860404360455504502169787883758544921875 -9.0554349332325525523401665850542485713958740234375,14.1363479465860422124023898504674434661865234375 -4.53543493323255209048738834098912775516510009765625,14.1363479465860422124023898504674434661865234375 -0.9354349332325517796249414459452964365482330322265625,0.81634794658604203920759800894302316009998321533203125 -0.9354349332325517796249414459452964365482330322265625,0.496347946586042088057411092449910938739776611328125 -0.615434933232551717452452066936530172824859619140625,0.476347946586042014782691467189579270780086517333984375 -0.5954349332325516996888836729340255260467529296875)))"  
```  
and  
```  
"MULTIPOLYGON(((2.286347946586042123584547880454920232295989990234375 -4.42543493323255088256473754881881177425384521484375,2.2863479465860425676737577305175364017486572265625 -0.9354349332325517796249414459452964365482330322265625,2.3563479465860428518908520345576107501983642578125 -0.9354349332325517796249414459452964365482330322265625,2.356347946586042407801642184494994580745697021484375 -4.42543493323255088256473754881881177425384521484375,2.286347946586042123584547880454920232295989990234375 -4.42543493323255088256473754881881177425384521484375)))"  
```  
  
However with those WKT, in the unit test, it works both in `double` and in `float` : [https://godbolt.org/z/fKPM3KsxT](https://godbolt.org/z/fKPM3KsxT)

---

## Comment 6

> Username: BenoitAndrieu  
> Created at: 2023-06-20 13:07:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1164#issuecomment-1598740478  

I reran some tests to help comprehension and summarize :  
  
- Full app :  
  - `float` without `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `FAIL`, wrong result, output is missing second parameter  
  - `float` with `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `OK`, correct single polygon in output  
  - `double` without `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `OK`, correct single polygon in output  
  - `double` with `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `OK`, correct single polygon in output  
  
- Unit test with same data **BUT** converted to / from WKT from `double` full app :  
  - `float` without `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `OK`, correct single polygon in output  
  - `float` with `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `OK`, correct single polygon in output  
  - `double` without `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `OK`, correct single polygon in output  
  - `double` with `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `OK`, correct single polygon in output  
  
- Unit test with same data **BUT** converted to / from WKT from `float` full app :  
  - `float` without `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `FAIL`, wrong result, output is missing second parameter  
  - `float` with `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : two polygons instead of one  
  - `double` without `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : two polygons instead of one  
  - `double` with `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` : `OK`  
  
```  
#define BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE  
#include <boost/geometry.hpp>  
  
#include <gtest/gtest.h>  
  
TEST(TestCaseName, TestFusionPolygone)  
{  
	using boost_coordinate_t = double;  
	using boost_point_2d = boost::geometry::model::d2::point_xy<boost_coordinate_t>;  
	using boost_polygon_2d = boost::geometry::model::polygon<boost_point_2d>;  
	using boost_multipolygon_2d = boost::geometry::model::multi_polygon<boost_polygon_2d>;  
  
	// wkt coming from double full app  
	{  
		auto big = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((0.476347946586042014782691467189579270780086517333984375 -0.5954349332325516996888836729340255260467529296875,14.4763479465860402939370032981969416141510009765625 -0.5954349332325516996888836729340255260467529296875,14.47634794658603851758016389794647693634033203125 -9.3954349332325488575179406325332820415496826171875,3.7363479465860418571310219704173505306243896484375 -9.3954349332325488575179406325332820415496826171875,3.5163479465860429939993991865776479244232177734375 -9.1754349332325499943863178486935794353485107421875,3.616347946586042638728031306527554988861083984375 -9.1754349332325499943863178486935794353485107421875,3.616347946586042638728031306527554988861083984375 -9.055434933232550775983327184803783893585205078125,8.3863479465860422124023898504674434661865234375 -9.055434933232550775983327184803783893585205078125,11.2863479465860425676737577305175364017486572265625 -9.0554349332325525523401665850542485713958740234375,14.1363479465860404360455504502169787883758544921875 -9.0554349332325525523401665850542485713958740234375,14.1363479465860422124023898504674434661865234375 -4.53543493323255209048738834098912775516510009765625,14.1363479465860422124023898504674434661865234375 -0.9354349332325517796249414459452964365482330322265625,0.81634794658604203920759800894302316009998321533203125 -0.9354349332325517796249414459452964365482330322265625,0.496347946586042088057411092449910938739776611328125 -0.615434933232551717452452066936530172824859619140625,0.476347946586042014782691467189579270780086517333984375 -0.5954349332325516996888836729340255260467529296875)))");  
		auto big_area = boost::geometry::area(big);  
  
		auto other = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((2.286347946586042123584547880454920232295989990234375 -4.42543493323255088256473754881881177425384521484375,2.2863479465860425676737577305175364017486572265625 -0.9354349332325517796249414459452964365482330322265625,2.3563479465860428518908520345576107501983642578125 -0.9354349332325517796249414459452964365482330322265625,2.356347946586042407801642184494994580745697021484375 -4.42543493323255088256473754881881177425384521484375,2.286347946586042123584547880454920232295989990234375 -4.42543493323255088256473754881881177425384521484375)))");  
		auto other_area = boost::geometry::area(other);  
  
		boost_multipolygon_2d union_result;  
		boost::geometry::union_(big, other, union_result);  
		auto union_area = boost::geometry::area(union_result);  
  
		EXPECT_EQ(union_result.size(), 1);  
		EXPECT_GT(union_area, big_area);  
  
		boost_multipolygon_2d intersection_result;  
		boost::geometry::intersection(big, other, intersection_result);  
  
		boost_multipolygon_2d intersection2_result;  
		boost::geometry::intersection(other, big, intersection2_result);  
	}  
  
	// wkt coming from float full app  
	{  
		auto big = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((0.4763479530811309814453125 -0.5954349040985107421875,14.47634792327880859375 -0.5954349040985107421875,14.47634792327880859375 -9.395435333251953125,3.7363479137420654296875 -9.395435333251953125,3.7163479328155517578125 -9.37543487548828125,3.5163478851318359375 -9.17543506622314453125,3.6163480281829833984375 -9.17543506622314453125,3.6163480281829833984375 -9.0554351806640625,14.13634777069091796875 -9.0554351806640625,14.13634777069091796875 -0.93543493747711181640625,2.3563480377197265625 -0.93543493747711181640625,2.286347866058349609375 -0.935434818267822265625,0.81634795665740966796875 -0.93543493747711181640625,0.6963479518890380859375 -0.815434932708740234375,0.4963479340076446533203125 -0.615434944629669189453125,0.4763479530811309814453125 -0.5954349040985107421875)))");  
		auto big_area = boost::geometry::area(big);  
  
		auto other = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((2.286347866058349609375 -4.42543506622314453125,2.286347866058349609375 -0.93543493747711181640625,2.3563480377197265625 -0.93543493747711181640625,2.3563480377197265625 -4.42543506622314453125,2.286347866058349609375 -4.42543506622314453125)))");  
		auto other_area = boost::geometry::area(other);  
  
		boost_multipolygon_2d union_result;  
		boost::geometry::union_(big, other, union_result);  
		auto union_area = boost::geometry::area(union_result);  
  
		EXPECT_EQ(union_result.size(), 1);  
		EXPECT_GT(union_area, big_area);  
  
		boost_multipolygon_2d intersection_result;  
		boost::geometry::intersection(big, other, intersection_result);  
  
		boost_multipolygon_2d intersection2_result;  
		boost::geometry::intersection(other, big, intersection2_result);  
	}  
}  
```

---

## Comment 7

> Username: barendgehrels  
> Created at: 2023-09-14 09:39:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1164#issuecomment-1719107696  

So it works with `double` and without `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE`  
this is the supported target of the library.  
  
Using `float` will give issues which are not always solveable (such as this one).  
If you don't mind I close the issue. If you find issues using `double`, you can report them.
