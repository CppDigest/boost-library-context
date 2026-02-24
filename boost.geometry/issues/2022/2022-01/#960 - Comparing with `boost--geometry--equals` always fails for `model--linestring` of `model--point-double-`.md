# #960 - Comparing with `boost::geometry::equals` always fails for `model::linestring` of `model::point<double>` [Closed]

> Username: iiiCpu  
> Created at: 2022-01-12 06:54:41 UTC  
> Updated at: 2022-05-18 05:35:20 UTC  
> Closed at: 2022-05-18 05:35:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/960  

Comparing `model::linestring` of `double` and `long double`  with `geometry::equals` always fails.   
  
```  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
    namespace bg = boost::geometry;  
    using point_i2 = bg::model::point<int, 2, bg::cs::cartesian>;  
    using point_f2 = bg::model::point<float, 2, bg::cs::cartesian>;  
    using point_d2 = bg::model::point<double, 2, bg::cs::cartesian>;  
  
    using line_i2 = bg::model::linestring<point_i2>;  
    using line_f2 = bg::model::linestring<point_f2>;  
    using line_d2 = bg::model::linestring<point_d2>;  
  
    std::string wkt_fd = "LINESTRING(107.518 202.138,107.519 200.003,106.620 198.024,105.165 196.957, 103.710 195.889)";  
    std::string wkt_i = "LINESTRING(107 202,107 200,106 198,105 196, 103 195)";  
			  
    line_i2 li1, li2;  
    line_f2 lf1, lf2;  
    line_d2 ld1, ld2;  
  
    bg::read_wkt(wkt_i, li1);  
    bg::read_wkt(wkt_i, li2);  
  
    bg::read_wkt(wkt_fd, lf1);  
    bg::read_wkt(wkt_fd, lf2);  
  
    bg::read_wkt(wkt_fd, ld1);  
    bg::read_wkt(wkt_fd, ld2);  
  
    bool eq_i = boost::geometry::equals(li1, li2);  
    //passed  
  
    bool eq_f = boost::geometry::equals(lf1, lf2);  
    //passed  
  
    bool eq_d = boost::geometry::equals(ld1, ld2);  
    //failed  
```  
  
Like, literally. Always. Even comparing value with itself.  
  
```  
    bool eq_d = boost::geometry::equals(ld1, ld1);  
    //failed  
```  
  
Boost Geometry v1.78  
MSVC 2017: cl.exe  ver 19.17.27045 for x86  
std = C++14

---

## Comment 1

> Username: iiiCpu  
> Created at: 2022-05-18 05:35:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/960#issuecomment-1129586003  

Seems to be solved in latest version? I'll close it for better or worse.
