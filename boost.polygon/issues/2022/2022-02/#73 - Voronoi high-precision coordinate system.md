# #73 - Voronoi high-precision coordinate system [Open]

> Username: todeschini-felipe  
> Created at: 2022-02-21 15:59:03 UTC  
> Updated at: 2022-02-21 15:59:03 UTC  
> Url: https://github.com/boostorg/polygon/issues/73  

Configuration:  
===  
Boost Version: `1.77`.  
GCC Version: `11.1.0`, `10.2.0`, and `9.3.0`.  
  
Problem:  
===  
While experimenting with [voronoi_advanced_tutorial.cpp](https://www.boost.org/doc/libs/1_78_0/libs/polygon/example/voronoi_advanced_tutorial.cpp) higher-precision coordinates (i.e. `long double` and `boost::int64_t`) I've noticed that the output diagram edges differs from the default coordinate system (`double` and `int`) for this particular case. The default produces the correct output, whereas the high-precision one explodes some values. I am using the tutorial as-is and changing only the voronoi builder inputs. I'm creating 3 polygons (4 segments per polygon).  
  
Full Testcase:  
===  
The following output images represent the input segments in black, and the output diagram edges in orange.  Figure (a) represent the input segments, Figure (b) shows voronoi output with the default coordinate system, whereas Figure (c) shows the output with the high-precision system.  
![image](https://user-images.githubusercontent.com/10549195/154988431-fd0fcc33-3415-4971-b015-7c2799d876f2.png)  
  
```  
// From voronoi_advanced_tutorial.cpp:  
#include <cmath>  
#include <cstdio>  
#include <string>  
// This will work properly only with GCC compiler.  
#include <ieee754.h>  
typedef long double fpt80;  
  
#include <boost/polygon/polygon.hpp>  
#include <boost/polygon/voronoi.hpp>  
using namespace boost::polygon;  
  
struct my_ulp_comparison {  
  enum Result {  
    LESS = -1,  
    EQUAL = 0,  
    MORE = 1  
  };  
  
  Result operator()(fpt80 a, fpt80 b, unsigned int maxUlps) const {  
    if (a == b)  
      return EQUAL;  
    if (a > b) {  
      Result res = operator()(b, a, maxUlps);  
      if (res == EQUAL) return res;  
      return (res == LESS) ? MORE : LESS;  
    }  
    ieee854_long_double lhs, rhs;  
    lhs.d = a;  
    rhs.d = b;  
    if (lhs.ieee.negative ^ rhs.ieee.negative)  
      return lhs.ieee.negative ? LESS : MORE;  
    boost::uint64_t le = lhs.ieee.exponent; le =  
        (le << 32) + lhs.ieee.mantissa0;  
    boost::uint64_t re = rhs.ieee.exponent; re =  
        (re << 32) + rhs.ieee.mantissa0;  
    if (lhs.ieee.negative) {  
      if (le - 1 > re)  
        return LESS;  
      le = (le == re) ? 0 : 1;  
      le = (le << 32) + lhs.ieee.mantissa1;  
      re = rhs.ieee.mantissa1;  
      return (re + maxUlps < le) ? LESS : EQUAL;  
    } else {  
      if (le + 1 < re)  
        return LESS;  
      le = lhs.ieee.mantissa0;  
      re = (le == re) ? 0 : 1;  
      re = (re << 32) + rhs.ieee.mantissa1;  
      return (le + maxUlps < re) ? LESS : EQUAL;  
    }  
  }  
};  
  
struct my_fpt_converter {  
  template <typename T>  
  fpt80 operator()(const T& that) const {  
    return static_cast<fpt80>(that);  
  }  
  
  template <std::size_t N>  
  fpt80 operator()(const typename detail::extended_int<N> &that) const {  
    fpt80 result = 0.0;  
    for (std::size_t i = 1; i <= (std::min)((std::size_t)3, that.size()); ++i) {  
      if (i != 1)  
        result *= static_cast<fpt80>(0x100000000ULL);  
      result += that.chunks()[that.size() - i];  
    }  
    return (that.count() < 0) ? -result : result;  
  }  
};  
  
// Voronoi diagram traits.  
struct my_voronoi_diagram_traits {  
  typedef fpt80 coordinate_type;  
  typedef voronoi_cell<coordinate_type> cell_type;  
  typedef voronoi_vertex<coordinate_type> vertex_type;  
  typedef voronoi_edge<coordinate_type> edge_type;  
  class vertex_equality_predicate_type {  
  public:  
    enum { ULPS = 128 };  
    bool operator()(const vertex_type &v1, const vertex_type &v2) const {  
      return (ulp_cmp(v1.x(), v2.x(), ULPS) == my_ulp_comparison::EQUAL &&  
              ulp_cmp(v1.y(), v2.y(), ULPS) == my_ulp_comparison::EQUAL);  
    }  
  private:  
    my_ulp_comparison ulp_cmp;  
  };  
};  
  
// Voronoi ctype traits for 48-bit signed integer input coordinates.  
struct my_voronoi_ctype_traits {  
  typedef boost::int64_t int_type;  
  typedef detail::extended_int<3> int_x2_type;  
  typedef detail::extended_int<3> uint_x2_type;  
  typedef detail::extended_int<128> big_int_type;  
  typedef fpt80 fpt_type;  
  typedef fpt80 efpt_type;  
  typedef my_ulp_comparison ulp_cmp_type;  
  typedef my_fpt_converter to_fpt_converter_type;  
  typedef my_fpt_converter to_efpt_converter_type;  
};  
  
// Tescase: The output from the high-precision coordinate system differs from the output with the default coordinate system.  
#define HIGH_PRECISION_MODE  
  
#ifdef HIGH_PRECISION_MODE  
using output_coordinate_t = fpt80;  
using input_coordinate_t = boost::int64_t;  
using VD = voronoi_diagram<output_coordinate_t, my_voronoi_diagram_traits>;  
using VB = voronoi_builder<input_coordinate_t, my_voronoi_ctype_traits>;  
#else  
using output_coordinate_t = double;  
using input_coordinate_t = int;  
using VD = voronoi_diagram<output_coordinate_t>;  
using VB = voronoi_builder<input_coordinate_t>;  
#endif  
  
using point_t = point_data<input_coordinate_t>;  
using segment_t = segment_data<input_coordinate_t>;  
  
static std::ostream &operator<<(std::ostream &os, const VD::vertex_type &pt) {  
  return os << "[" << pt.x() << "," << pt.y() << "]";  
}  
  
int main() {  
  VB vb;  
  // Creates segments from a sequence of points (circular).  
  auto add_segment_to_vb = [&](const std::vector<point_t> &vec) {  
    for (std::size_t i = 0; i < vec.size(); i++) {  
      auto p0 = vec[i];  
      auto p1 = vec[(i + 1) % vec.size()];  
      vb.insert_segment(p0.x(), p0.y(), p1.x(), p1.y());  
    }  
  };  
  
  add_segment_to_vb(std::vector<point_t>{{0, 0}, {0, 200}, {200, 200}, {200, 0}}); // major rectangle  
  add_segment_to_vb(std::vector<point_t>{{20, 20}, {80, 20}, {80, 180}, {20, 180}}); // left inner rectangle  
  add_segment_to_vb(std::vector<point_t>{{120, 20}, {180, 20}, {180, 180}, {120, 180}}); // right inner rectangle  
  
  VD vd;  
  vb.construct(&vd);  
  
  for (const auto& edge : vd.edges()) {  
    if (edge.is_primary() && edge.is_finite())  
      std::cout << *edge.vertex0() << " -> " << *edge.vertex1() << std::endl;  
  }  
  return 0;  
}  
```  
  
I've reduce the test case to three and two segments that show different outputs when changing the coordinate system. The "most severe" is with all segments.  
  
Three Segments Testcase  
===  
The snippet below show the input segments to voronoi builder. Figure (d) depicts the output from the default coordinate system, whereas Figure (e) from the high-precision.  
```  
vb.insert_segment(200, 0, 200, 200);  
vb.insert_segment(200, 200, 0, 200);  
vb.insert_segment(180, 180, 120, 180);  
```  
![image](https://user-images.githubusercontent.com/10549195/154986757-d7a0abb1-ff3b-4114-bd78-4b278ed55349.png)  
  
Two Segments Testcase  
===  
Similarly, the snippet shows the input segments, Figure (f) the output from the default system, and Figure (g) from the high-precision.  
```  
vb.insert_segment(200, 0, 200, 200);  
vb.insert_segment(180, 180, 120, 180);  
```  
![image](https://user-images.githubusercontent.com/10549195/154986826-33b7ef1e-16c0-4580-8f70-8346731c02f7.png)  
  
Remarks  
===  
I can't figure out what's the problem and the `int_type` to `ftp_type` conversion from the tutorial seems correct. Could you assist investigating possible causes that are causing this behavior?
