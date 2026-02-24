# #861 - Incorrect results for intersection in floating point edge cases [Closed]

> Username: rgreenblatt  
> Created at: 2021-06-05 17:56:17 UTC  
> Updated at: 2021-06-19 11:42:31 UTC  
> Closed at: 2021-06-19 11:42:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/861  

This is a case where triangles share an edge and then the results are very wrong:  
```c++  
#include <boost/geometry.hpp>  
  
#include <fstream>  
#include <iostream>  
  
int main() {  
  using Point = boost::geometry::model::d2::point_xy<double>;  
  using Poly = boost::geometry::model::polygon<Point>;  
  Poly a{{  
      {0.000000000000000000000000000000, 1.000000000000000000000000000000},  
      {1.000001713207607556554989969300, -0.000001713207607420577826331251},  
      {0.000000000000000000000000000000, 0.230455148580528512702869647910},  
      {0.000000000000000000000000000000, 1.000000000000000000000000000000},  
  }};  
  Poly b{{  
      {0.999994299999858893279736093973, 0.000000000000000000000000000000},  
      {0.239056281157559169514570385218, 0.000000000000000000000000000000},  
      {0.073239184141848157061360780062, 0.213576637616646664019626200570},  
      {0.999994299999858893279736093973, 0.000000000000000000000000000000},  
  }};  
  
  std::vector<Poly> polys;  
  boost::geometry::intersection(a, b, polys);  
  
  std::ofstream svg("bad_intersection.svg");  
  boost::geometry::svg_mapper<Point> mapper(svg, 400, 400);  
  
  mapper.add(a);  
  mapper.add(b);  
  mapper.add(polys[0]);  
  
  mapper.map(a, "fill-opacity:0.3;fill:rgb(51,51,153);stroke:rgb(51,51,153);"  
                "stroke-width:2");  
  mapper.map(b, "fill-opacity:0.3;fill:rgb(51,153,51);stroke:rgb(51,153,51);"  
                "stroke-width:2");  
  mapper.map(polys[0],  
             "fill-opacity:0.3;fill:rgb(153,51,51);stroke:rgb(153,51,51);"  
             "stroke-width:2");  
  
  std::cout << boost::geometry::wkt(b) << std::endl;  
  std::cout << boost::geometry::wkt(polys[0]) << std::endl;  
}  
```  
This results in:  
```  
POLYGON((0.999994 0,0.239056 0,0.0732392 0.213577,0.999994 0))  
POLYGON((0.999994 0,0.239056 0,0.0732392 0.213577,0.999994 0))  
```  
  
![bad_intersection](https://user-images.githubusercontent.com/8813096/120900827-23a3b700-c605-11eb-9d80-529533205a22.png)  
So despite `a` and `b` barely overlapping, the intersection is equal to `b` (the bottom triangle is both `b` and `polys[0]`).

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-06-16 09:24:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/861#issuecomment-862201547  

Please compile the library with `BOOST_GEOMETRY_NO_ROBUSTNESS`  
This will be the default, hopefully from next version on.  
It then does not use rescaling and this bug is then fixed.  
I will add it to the testsuite.  
Thanks for the report.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-06-19 11:42:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/861#issuecomment-864395150  

I added the testcase, it will be merged soon. Closing.
