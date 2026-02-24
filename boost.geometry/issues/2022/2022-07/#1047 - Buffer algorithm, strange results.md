# #1047 - Buffer algorithm, strange results? [Closed]

> Username: ghost  
> Created at: 2022-07-31 20:04:17 UTC  
> Updated at: 2022-08-17 09:53:48 UTC  
> Closed at: 2022-08-17 09:53:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1047  

Hi,  
  
Boost 1.79, Debian.  
  
  
Running this code:  
```  
using Vector2d = boost::geometry::model::d2::point_xy <double, boost::geometry::cs::cartesian>;  
using mpol2d = boost::geometry::model::multi_polygon <boost::geometry::model::polygon <Vector2d, true, false> >;  
using pol2d = boost::geometry::model::polygon <Vector2d, true, false>;  
  
pol2d p;  
  
p.outer().push_back( {0, 0} );  
p.outer().push_back( {2, 0} );  
p.outer().push_back( {2, 2} );  
p.outer().push_back( {0, 2} );  
  
boost::geometry::correct( p );  
  
mpol2d p2;  
  
p2.push_back( p );  
  
// just buffer the shape and return the inner line  
boost::geometry::strategy::buffer::side_straight side_strategy;  
boost::geometry::strategy::buffer::point_square point_strategy;  
boost::geometry::strategy::buffer::join_miter join_strategy;  
boost::geometry::strategy::buffer::end_flat end_strategy;  
boost::geometry::strategy::buffer::distance_asymmetric <double> distance_strategy( 1.0, 1.0 );  
boost::geometry::strategy::buffer::distance_asymmetric <double> distance_strategy2( 1.0, 2.0 );  
mpol2d result, r2;  
  
boost::geometry::buffer( p2, result, distance_strategy, side_strategy, join_strategy, end_strategy, point_strategy );  
boost::geometry::buffer( p2, r2, distance_strategy2, side_strategy, join_strategy, end_strategy, point_strategy );  
```  
  
I get the same multi polygon for both result and r2.  
Also, I played with different values, 0.1, 0.2, only the left value is active, the right value doesn't seem to affect the outcome.  
  
Plus: how do we know which is left side or right side: in a closed polygon, left seems to be the interior and right the exterior but not sure. (Am I right: it could be related to the winding order, and nullifying the azimuth always gives the above remark).  
  
Thanks,

---

## Comment 1

> Username: ghost  
> Created at: 2022-08-01 07:17:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1047#issuecomment-1200810574  

But the symmetric version works as expected

---

## Comment 2

> Username: barendgehrels  
> Created at: 2022-08-17 09:04:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1047#issuecomment-1217717610  

**Point 1:**  
About `using pol2d = boost::geometry::model::polygon <Vector2d, true, false>;`  
Because you specify the polygon type as clockwise (first `true`), the right side is the interior and the left side is the exterior.  
  
So the distance strategy works in a clockwise way and the left value (always `1.0` in your example) will be the effective buffer distance.  
  
You might maybe expect that if you make the polygon anticlockwise, the right value would be used, but that is not true. Internally it uses the clockwise buffer and reverses the result later. We cannot change that anymore (code would behave differently for current users, and besides that it's not important because of point 2).  
  
**Point 2** asymmetric buffers are only useful for linear features (linestrings). Then the left value will be used for the left side, and the right side will be used for the right side. For points and (multi)polygons, it's not important. Also for interior rings (holes), the left side is used because in a clockwise polygon, the hole is anticlockwise and the interior is still on the right side. So the left side is where the buffer is.

---

## Comment 3

> Username: ghost  
> Created at: 2022-08-17 09:53:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1047#issuecomment-1217787506  

Thanks for the clarification 👍
