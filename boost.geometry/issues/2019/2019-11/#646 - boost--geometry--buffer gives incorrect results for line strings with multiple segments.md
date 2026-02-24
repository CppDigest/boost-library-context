# #646 - boost::geometry::buffer gives incorrect results for line strings with multiple segments [Closed]

> Username: mark-grimes  
> Created at: 2019-11-19 11:40:33 UTC  
> Updated at: 2019-11-19 18:52:42 UTC  
> Closed at: 2019-11-19 12:07:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/646  

I'm getting completely wrong results from `buffer` for linestrings with more than one segment.  I've tried this with:  
* 1.70.0  
* 1.71.0  
* boost 1.70.0 with the head of boost geometry copied on top (not sure how sensible this is, but it compiles fine)  
  
I've managed to reproduce with this minimal example:  
```cpp  
constexpr double buffer=10;  
  
using point_type=boost::geometry::model::point<double,2,boost::geometry::cs::geographic<boost::geometry::degree>>;  
using linestring_type=boost::geometry::model::linestring<point_type>;  
using polygon_type=boost::geometry::model::polygon<point_type>;  
using multi_polygon_type=boost::geometry::model::multi_polygon<polygon_type>;  
  
linestring_type input{ {-1781.721057, 3979.354339}, {-1716.598109, 3699.048253}, {-1715.308642, 3695.917025} };  
  
const boost::geometry::strategy::buffer::distance_symmetric<double> distance(buffer);  
const boost::geometry::strategy::buffer::side_straight side_strategy;  
const boost::geometry::strategy::buffer::join_round join_strategy;  
const boost::geometry::strategy::buffer::end_round end_strategy;  
const boost::geometry::strategy::buffer::point_circle point_strategy;  
  
multi_polygon_type multiResult;  
boost::geometry::buffer( input, multiResult, distance, side_strategy, join_strategy, end_strategy, point_strategy );  
  
for( const auto& polygon : multiResult )  
{  
    printf( "Results for polygon\n" );  
    for( const auto& point : polygon.outer() )  
    {  
        printf( "\t%f, %f\n", boost::geometry::get<0>(point), boost::geometry::get<1>(point) );  
    }  
}  
```  
Gives the following (abreviated) output:  
```  
Results for polygon  
	-16.692056, 76.872441  
	-1771.980482, 3981.617347  
	...snip...  
	-1791.461632, 3977.091331  
	-16.692056, 76.872441  
```  
The first and last points are clearly nowhere near the original line string.  
  
The actual use case we have is in geographic coordinates, but we were having issues so did an orthographic projection with the origin at the centre of the area of interest. All the incorrect points are clustered close to the origin. If we change the origin to the south west of the area of interest (so all projected points are positive) the incorrect points shift to be clustered around the new origin.  So it would appear the points are produced with absolute values too small.  
  
Linestrings with a single segment appear to be okay.  
  
Am I using an incorrect configuration of strategies? As far as I can tell this is exactly as the [example code](https://www.boost.org/doc/libs/1_71_0/libs/geometry/doc/html/geometry/reference/algorithms/buffer/buffer_7_with_strategies.html) except that our values are a few orders larger.

---

## Comment 1

> Username: mark-grimes  
> Created at: 2019-11-19 11:56:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/646#issuecomment-555475665  

Using `end_flat` strategy gives an interesting result (full print out):  
```  
Results for polygon  
	-16.692056, 76.872441  
	-1771.980482, 3981.617347  
	-1791.461632, 3977.091331  
	-16.692056, 76.872441  
```  
I'd expect a buffer around a two segment line string to have 7 points (6+1 repeated).  Four coordinates describes a triangle, which is a result I really don't understand.

---

## Comment 2

> Username: mark-grimes  
> Created at: 2019-11-19 12:07:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/646#issuecomment-555479138  

Apologies, I've just realised that in boiling down to a minimal example I incorrectly used a geographic coordinate.  I think the issue still exists but I'll close until I verify.

---

## Comment 3

> Username: mark-grimes  
> Created at: 2019-11-19 12:17:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/646#issuecomment-555482228  

There is no problem. In the full code we use a custom type which was accidentally set as geographic thanks to a bad cut and paste job when converting to cartesian. So this can stay closed.  
  
Apologies.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2019-11-19 18:52:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/646#issuecomment-555653922  

OK, good to hear it works as expected. Thanks for the follow up.
