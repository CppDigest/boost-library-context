# #364 - registered types vs existing models: within function compilation error [Closed]

> Username: daniel-j-h  
> Created at: 2016-09-10 21:26:15 UTC  
> Updated at: 2016-12-27 15:39:38 UTC  
> Closed at: 2016-12-27 15:39:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/364  

I have the following use-case: check if a linestring is within a polygon.  
Coordinate system is Spherical Equatorial.  
  
The [support-matrix in the documentation](http://www.boost.org/doc/libs/1_61_0/libs/geometry/doc/html/geometry/reference/algorithms/within/within_2.html) says this should work.  
  
When I use the existing Boost.Geometry linestring and polygon models the  
  
```  
within(linestring, polygon)  
```  
  
function call compiles fine. But when I'm switching out the existing Boost.Geometry linestring model with my own type (which I registered as a linestring) compilation fails.  
  
Here is a small self-contained test case reproducing this issue:  
  
``` cpp  
#include <vector>  
  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <boost/geometry/geometries/register/point.hpp>  
#include <boost/geometry/geometries/register/linestring.hpp>  
  
struct myPoint { float x, y; };  
struct myLine { std::vector<myPoint> pts; };  
  
BOOST_GEOMETRY_REGISTER_POINT_2D(myPoint, float, cs::spherical_equatorial<degree>, x, y)  
BOOST_GEOMETRY_REGISTER_LINESTRING(std::vector<myPoint>)  
  
using bgPoint = boost::geometry::model::point<float, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::degree>>;  
using bgPoly = boost::geometry::model::polygon<bgPoint>;  
  
int main() {  
  myLine ln;  
  bgPoly ply;  
  
  boost::geometry::within(ln.pts, ply);  // does not compile  
  
  // but this does, although I expected this to be exactly the same  
  //  
  //using bgLine = boost::geometry::model::linestring<bgPoint>;  
  //bgLine ln2;  
  //boost::geometry::within(ln2, ply);  
}  
```  
  
This is surprising to me since both types are spherical equatorial, float precision, degree unit. There is no semantic difference from what I can see. I expected them to behave identically.  
  
Here is the full error log. Boost version is 1.62 beta1.  
https://gist.github.com/daniel-j-h/64bae0c01caaff30c2e40d27f7a98406

---

## Comment 1

> Username: awulkiew  
> Created at: 2016-12-27 15:06:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/364#issuecomment-269337600  

Thanks!  
Fix: https://github.com/boostorg/geometry/commit/c5b746829159484a6f453a6361b35822f8d8268c
