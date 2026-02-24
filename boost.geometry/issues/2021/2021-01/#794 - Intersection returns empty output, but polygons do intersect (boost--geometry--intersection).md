# #794 - Intersection returns empty output, but polygons do intersect (boost::geometry::intersection) [Closed]

> Username: beekm25  
> Created at: 2021-01-15 14:02:08 UTC  
> Updated at: 2021-02-23 10:51:38 UTC  
> Closed at: 2021-02-23 10:51:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/794  

Both polygons are valid, and they intersect but the output of intersections is empty. Tested it using the example on the intersection documentation page.   
  
On windows 10, VS2017 and Intel 18 compiler. Boost version is 1.75.0.  
  
Example with coordinates, and picture in google earth:   
```  
#include <iostream>  
#include <deque>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
#include <boost/foreach.hpp>  
  
int main()  
{  
	typedef boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double> > polygon;  
  
	polygon green, blue;  
  
	boost::geometry::read_wkt(  
		"POLYGON"  
		"((5.86471679380106 51.8443007952212, 5.86457625031471 51.8442235859994, 5.86452595889568 51.8442558989143, 5.86468988435745 51.844345239633, 5.86471679380106 51.8443007952212))", green );  
	  
	boost::geometry::read_wkt(  
		"POLYGON"  
		"((5.8646785956956 51.8438148218628, 5.86456848904512 51.8438445419308, 5.86455079991675 51.8438866292232, 5.86448834669633 51.8439025109228, 5.8638489350605 51.843739323012, 5.86380402974754 51.8437178379172, 5.86375797862619 51.8436728801712, 5.86366085369074 51.8436666289446, 5.86356537284674 51.843677302062, 5.86337245127205 51.8437325507295, 5.86313902227857 51.8438396665712, 5.86288697731946 51.8439887446913, 5.8626092222089 51.8441797645951, 5.86260361009903 51.8441933284478, 5.86265480097024 51.8442371256246, 5.86286226200901 51.8443882880638, 5.86334168937117 51.8446493842117, 5.86357121339959 51.8447939467362, 5.86404880071713 51.8451784822382, 5.86436332066895 51.8449557437605, 5.86463358282339 51.8444382290734, 5.86483681629208 51.8441025623161, 5.86479901277289 51.8439849814262, 5.86468189579615 51.8438156075405, 5.8646809 51.8438142, 5.8646785956956 51.8438148218628))", blue );  
  
	std::deque<polygon> output;  
	boost::geometry::intersection( green, blue, output );  
  
	int i = 0;  
	std::cout << "green && blue:" << std::endl;  
	BOOST_FOREACH( polygon const& p, output )  
	{  
		std::cout << i++ << ": " << boost::geometry::area( p ) << std::endl;  
	}  
	  
	return 0;  
}  
```  
![Untitled](https://user-images.githubusercontent.com/8014631/104735495-f2077d80-5741-11eb-837a-60cf11191e27.png)

---

## Comment 1

> Username: vissarion  
> Created at: 2021-01-15 15:52:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/794#issuecomment-761022231  

Please include more information in your issue report, such as, boost version and your platform setup.  
  
I can reproduce this behaviour, with boost `develop` branch and `clang-9` on ubuntu `18.04`.  
  
The issue is maybe some floating point error in computation of `intersection` algorithm.  
  
Note that on my side the code computes the correct result by using boost multiprecission.

---

## Comment 2

> Username: beekm25  
> Created at: 2021-01-21 14:07:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/794#issuecomment-764665877  

Thanks for the reply. I've added some more information about my setup. Could you elaborate a bit on how you used multiprecission in this scenario?

---

## Comment 3

> Username: beekm25  
> Created at: 2021-01-21 16:17:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/794#issuecomment-764761687  

After some research into multiprecision I've found the following dat seem to work. Instead of using double, the polygons use cpp_bin_float_single, like this:  
  
`  
typedef boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<cpp_bin_float_single> > polygon;  
`

---

## Comment 4

> Username: barendgehrels  
> Created at: 2021-01-29 16:37:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/794#issuecomment-769912790  

@beekm25 can you add a comment why it is reopened?

---

## Comment 5

> Username: beekm25  
> Created at: 2021-02-16 11:57:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/794#issuecomment-779790086  

Sorry for the late response. I reopened it because in my opinion multiprecision is not really a fix, more a workaround. I’ve noticed an enormous performance reduction using multiprecision, to the point it isn’t usable for me anymore.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2021-02-16 20:39:41 UTC  
> Updated at: 2021-02-16 20:40:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/794#issuecomment-780104141  

Right, indeed multiprecision is slower.  
  
Can you please try with the define `BOOST_GEOMETRY_NO_ROBUSTNESS`  
  
This will become the default in a future version.

---

## Comment 7

> Username: beekm25  
> Created at: 2021-02-23 10:51:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/794#issuecomment-784111849  

@barendgehrels Thanks, this seems to work right. I've checked the provided example as well as the rest of my polygons. Performance-wise it feels a bit faster than ‘normal’ with just a double, is this correct? I didn’t actually measured it though.
