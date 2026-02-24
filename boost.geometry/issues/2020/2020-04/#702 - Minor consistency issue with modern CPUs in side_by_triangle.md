# #702 - Minor consistency issue with modern CPUs in side_by_triangle [Open]

> Username: tinko92  
> Created at: 2020-04-23 00:25:36 UTC  
> Updated at: 2020-04-23 00:25:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/702  

While reading the code for side_by_triangle, I noticed that there is some kind of normalization of the order of points for consistency purposes as described in the comment starting in this line: https://github.com/boostorg/geometry/blob/56a9f79036dc3bce8dcd0483cfa728a196997f81/include/boost/geometry/strategies/cartesian/side_by_triangle.hpp#L189  
  
GCC will break this effort when it automatically introduces the FMA instruction to the side_value calculation. Consider this following test program:  
  
`  
#include <iostream>  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
using p = bg::model::d2::point_xy<double>;  
  
int main() {  
        double x1, x2, x3, y1, y2, y3;  
        std::cin >> x1;  
        std::cin >> y1;  
        std::cin >> x2;  
        std::cin >> y2;  
        std::cin >> x3;  
        std::cin >> y3;  
        p p1(x1, y1), p2(x2, y2), p3(x3, y3);  
        std::cout << bg::strategy::side::  
                side_by_triangle<double>::apply(p1, p2, p3) << "\n";  
        std::cout << bg::strategy::side::  
                side_by_triangle<double>::apply(p1, p3, p2) << "\n";  
        return 0;  
}  
`  
  
with the following inputs:  
  
`  
-543.20354630837983   
479.29724674268812  
-63.906299565691747   
958.59449348537623  
-862.73504413683861   
159.76574891422936  
`  
  
when compiled with GCC with -march=native and -O2 (or -O3) with a modern CPU (I think everything since Haswell in 2013), this program will print -1, -1, which is inconsistent. When compiled with -mno-fma it will instead print 0, 0, which is untrue (1, -1 would be correct) but consistent.  
  
Three possible solutions come to mind:  
- totally sort the input points but this would add even more branches than there are already  
- tell GCC to compile that specific function with -mno-fma but I don't know how portable that would be (I do not have any proprietary compilers to test compatibility)  
- Ignore the issue because it should manifest for almost-collinear input points
