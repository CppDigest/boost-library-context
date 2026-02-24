# #804 - buffer stack overflow with BOOST_GEOMETRY_NO_ROBUSTNESS [Closed]

> Username: amagdum  
> Created at: 2021-02-11 18:12:57 UTC  
> Updated at: 2021-06-03 14:43:07 UTC  
> Closed at: 2021-05-12 09:24:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/804  

I am using BOOST_GEOMETRY_NO_ROBUSTNESS to improve results for boolean operation. But then I came across a regression in buffer operation. Buffer internally uses partition and that seems to cause stack overflow. I can see that level in partition.hpp hits 100 but if robustness is used that level is much lower. In standalone application it does not cause stack overflow but in real application it does since stack limit is reached. Below is exact use case:  
  
```cpp  
const std::string polyline_wkt = "LINESTRING(3426.81164664899552 8938.53661185019882,3426.62883927617713 8939.51976060010384,3426.44603190335874 8940.50290935001067,3426.23100079147753 8941.47707092313613,3425.87860097211251 8942.4129204662313,3425.45802480437851 8943.30567872837673,3424.74208442957797 8944.00101496333809,3423.94645488358219 8944.60679844407787,3423.10530295595527 8945.10445162063661,3422.1159708275427 8945.25012914014769,3421.13075124514307 8945.30157565321315,3420.157872279618 8945.07026089268402,3419.21662385945774 8944.76209894987915,3418.36279996540497 8944.24153698628106,3417.55289446703637 8943.66587847006485,3416.96754145472914 8942.88254823527313,3416.66921895795213 8941.92808310342298,3416.42352704107179 8940.96245781928337,3416.39904857505007 8939.97200417166641,3416.57828590827057 8938.9881983074938,3416.75752324149107 8938.00439244332119,3416.93676057471157 8937.02058657914858,3416.95909120500437 8936.89801722019911)";  
  
using Point2d =  
    boost::geometry::model::d2::point_xy<double>  
boost::geometry::polyline<Point2d> polyline;  
boost::geometry::read_wkt(polyline_wkt, polyline);  
  
const double left_buffer_distance = 4.0;  
  const double right_buffer_distance = 4.0;  
  constexpr int kBufferPointsPerCycle = 36;  
using Polygon2d = :boost::geometry::model::polygon<Point2d>;  
:boost::geometry::model::multi_polygon<Polygon2d> result;  
  
  boost::geometry::strategy::buffer::distance_asymmetric<double>  
      distance_strategy(left_buffer_distance, right_buffer_distance);  
  boost::geometry::strategy::buffer::join_round join_strategy(  
      kBufferPointsPerCycle);  
  boost::geometry::strategy::buffer::end_flat end_strategy;  
  boost::geometry::strategy::buffer::point_circle circle_strategy(  
      kBufferPointsPerCycle);  
  boost::geometry::strategy::buffer::side_straight side_strategy;  
  boost::geometry::buffer(polyline, result, distance_strategy,  
                          side_strategy, join_strategy, end_strategy,  
                          circle_strategy);  
```  
  
Boost version I am using is 1.65 (looking at code likely reproducible even with latest?) and it's on Linux platform.

---

## Comment 1

> Username: mloskot  
> Created at: 2021-02-11 18:46:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/804#issuecomment-777708334  

@amagdum Please, try the latest Boost 1.75 before going any further, thanks.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-05-12 09:24:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/804#issuecomment-839615789  

There have been a problem in the past with partition and BOOST_GEOMETRY_NO_ROBUSTNESS but that is fixed, I believe in 1.75  
I just tested this issue and the level goes to 1, so I will close this issue.  
  
BTW the provided code was incorrect in several places (for example we have no `polyline`), here a correct version  
https://godbolt.org/z/4hfTff5fP
