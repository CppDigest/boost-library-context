# #1382 - Buffer operation creates self-intersection [Closed]

> Username: gastald88  
> Created at: 2025-03-27 09:14:31 UTC  
> Updated at: 2025-04-25 17:48:53 UTC  
> Closed at: 2025-04-22 06:51:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1382  

After applying the buffer function on a polygon, it turns invalid because of self-intersection.   
My platform toolset is Visual Studio 2019 (v142) and the Boost version is 1.87.0. Below a code to replicate the problem, let me know if further details are needed, thank you.  
```C++  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <iostream>  
  
namespace bg = boost::geometry;  
using point_t = bg::model::point<double, 2, bg::cs::cartesian>;  
using polygon_t = bg::model::polygon<point_t>;  
using namespace bg::strategy::buffer;  
typedef bg::model::multi_polygon<polygon_t> GPolygon;  
  
GPolygon apply_buffer(const GPolygon& poly, double amount)  
{  
    //buffer parameters  
    bg::strategy::buffer::distance_symmetric<double> distance_strategy(amount);  
    end_round end_strategy;  
    point_circle circle_strategy(10);  
    side_straight side_strategy;  
    join_miter join_strategy;  
  
    //apply buffering  
    GPolygon buffered_poly;  
    bg::buffer(poly, buffered_poly, distance_strategy, side_strategy,  
        join_strategy, end_strategy, circle_strategy);  
  
    return buffered_poly;  
}  
  
  
int main() {  
    std::cout << "Using Boost "  
        << BOOST_VERSION / 100000 << "."  // maj. version  
        << BOOST_VERSION / 100 % 1000 << "."  // min. version  
        << BOOST_VERSION % 100                // patch version  
        << std::endl;  
  
    //polygon to buffer  
    GPolygon poly;  
    bg::read_wkt("MULTIPOLYGON (((406.559 1175.2228, 406.559 1175.2021, 406.5736 1175.1874, 407.2463 1174.5148, 407.261 1174.5002, 407.2817 1174.5002, 408.2363 1174.5002, 408.257 1174.5002, 408.2717 1174.5148, 408.9444 1175.1874, 408.959 1175.2021, 408.959 1175.2228, 408.959 1187.8874, 409.2168 1188.8488, 409.9112 1189.5429, 410.8728 1189.8002, 415.3832 1189.8002, 416.8753 1189.1045, 426.059 1178.1597, 426.059 1176.5002, 426.109 1176.5002, 429.6457 1176.5002, 430.6072 1176.2426, 431.3014 1175.5484, 431.559 1174.5869, 431.559 1162.5002, 431.609 1162.5002, 436 1162.5002, 436 1121.7002, 408.559 1121.7002, 408.559 1121.6502, 408.559 1059.3005, 406.559 1059.3005, 406.559 1059.2505, 406.559 1027.1505, 406.559 1027.1005, 406.609 1027.1005, 431.559 1027.1005, 431.559 999.8005, 431.609 999.8005, 436 999.8005, 436 737.6011, 431.559 737.6011, 431.559 737.5511, 431.559 703.6011, 426.109 703.6011, 426.059 703.6011, 426.059 703.5511, 426.059 688.4511, 426.059 688.4011, 426.109 688.4011, 431.559 688.4011, 431.559 674.4011, 431.609 674.4011, 436 674.4011, 436 633.6011, 407.2614 633.6011, 407.2467 633.5865, 406.5736 632.9133, 406.559 632.8986, 406.559 632.8779, 406.559 631.9242, 406.559 631.9035, 406.5736 631.8888, 407.2467 631.2157, 407.2614 631.2011, 407.2821 631.2011, 408.559 631.2011, 408.559 579.0425, 405.6425 576.126, 380 576.126, 380 1200, 406.559 1200, 406.559 1175.2228)))", poly);  
  
    std::cout << std::boolalpha  
        << "is poly valid? " << bg::is_valid(poly)  
        << std::endl << std::endl;  
  
    double amount;  
    GPolygon buffered_poly;  
    std::string message;  
    bool buffered_poly_valid;  
  
  
    amount = 0.05;  
    buffered_poly = apply_buffer(poly, amount);  
  
    //test validity  
    buffered_poly_valid = boost::geometry::is_valid(buffered_poly, message);  
  
    std::cout << "buffer amount: " << amount << std::endl  
        << "is buffered poly valid? " << buffered_poly_valid << std::endl  
        << "validity check message: " << message  
        << std::endl << std::endl;  
}  
```  
  
output is:  
```  
Using Boost 1.87.0  
is poly valid? true  
  
buffer amount: 0.05  
is buffered poly valid? false  
validity check message: Geometry has invalid self-intersections. A self-intersection point was found at (426.109, 1176.55); method: i; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 0, -1, 25}/{0, 0, -1, 28}  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2025-04-19 17:41:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1382#issuecomment-2816797617  

Thanks!   
  
I can reproduce it.  
  
But good news: #1369 fixes this issue. New output is:  
  
```  
is poly valid? true  
  
buffer amount: 0.05  
is buffered poly valid? true  
validity check message: Geometry is valid  
```

---

## Comment 2

> Username: gastald88  
> Created at: 2025-04-19 20:18:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1382#issuecomment-2816852565  

Wonderful, thank you!

---

## Comment 3

> Username: barendgehrels  
> Created at: 2025-04-25 17:48:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1382#issuecomment-2831054395  

It's merged and will be available in Boost 1.89  
Thanks for closing
