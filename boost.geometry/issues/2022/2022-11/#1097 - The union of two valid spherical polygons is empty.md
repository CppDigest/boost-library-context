# #1097 - The union of two valid spherical polygons is empty [Open]

> Username: rconde01  
> Created at: 2022-11-21 15:25:47 UTC  
> Updated at: 2022-11-29 15:01:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1097  

The example program is below.  We accumulate a polygon by rotating a cone around z and gradually moving outward.  
  
```  
#include <array>  
#include <cmath>  
#include <iostream>  
#include <stdexcept>  
#include <vector>  
  
#include <Eigen/Dense>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
namespace bg = boost::geometry;  
  
using SphericalPoint =  
   bg::model::d2::point_xy<double, bg::cs::spherical_equatorial<bg::degree>>;  
  
using SphericalPolygon = boost::geometry::model::polygon<  
   SphericalPoint,  
   false,   // counter-clockwise  
   true,    // closed  
   std::vector,  
   std::vector,  
   std::allocator,  
   std::allocator>;  
  
auto rad(double deg) -> double {  
   return M_PI * deg / 180.0;  
}  
  
auto deg(double rad) -> double {  
   return 180.0 * rad / M_PI;  
}  
  
auto create_truncated_sphere_unit_vectors(double cone_half_angle_deg)  
   -> std::vector<std::array<double, 3>> {  
   auto const ring_radius = sin(rad(cone_half_angle_deg));  
   auto const ring_height = cos(rad(cone_half_angle_deg));  
  
   std::vector<std::array<double, 3>> result;  
  
   auto const num_ring_points = 361;  
  
   result.reserve(num_ring_points);  
  
   for (size_t i = 0; i < num_ring_points - 1; ++i) {  
      auto const angle_deg = (360.0 * i) / num_ring_points;  
      result.push_back(  
         {ring_radius * cos(rad(angle_deg)),  
          ring_radius * sin(rad(angle_deg)),  
          ring_height});  
   }  
  
   result.push_back(result.front());  
  
   return result;  
}  
  
auto rotate_about_axis(  
   std::array<double, 3> const& axis,  
   double                       rot_angle_deg,  
   std::array<double, 3> const& vec) -> std::array<double, 3> {  
   Eigen::Vector3d eig_axis(axis[0], axis[1], axis[2]);  
   Eigen::Vector3d eig_vec(vec[0], vec[1], vec[2]);  
  
   auto const rot_angle_rad = rad(rot_angle_deg);  
  
   Eigen::Vector3d result = Eigen::AngleAxisd(rot_angle_rad, eig_axis) * eig_vec;  
  
   return {result[0], result[1], result[2]};  
}  
  
auto rotate_ring_about_axis(  
   std::array<double, 3> const&              axis,  
   double                                    rot_angle_deg,  
   std::vector<std::array<double, 3>> const& ring) -> std::vector<std::array<double, 3>> {  
   std::vector<std::array<double, 3>> result;  
  
   result.reserve(ring.size());  
  
   for (auto const& o : ring)  
      result.push_back(rotate_about_axis(axis, rot_angle_deg, o));  
  
   return result;  
}  
  
auto unit_vector_to_spherical_point(std::array<double, 3> const& unit_vector)  
   -> SphericalPoint {  
   auto calculate_theta = [](auto const& vec) {  
      if (vec[0] == 0.0 && vec[1] == 0.0)  
         return 0.0;  
  
      return deg(atan2(vec[1], vec[0]));  
   };  
  
   return SphericalPoint(calculate_theta(unit_vector), deg(asin(unit_vector[2])));  
}  
  
auto unit_vectors_to_spherical_polygon(  
   std::vector<std::array<double, 3>> const& unit_vectors) -> SphericalPolygon {  
   SphericalPolygon result;  
  
   auto& outer = result.outer();  
  
   outer.resize(unit_vectors.size());  
  
   for (size_t i = 0; i < unit_vectors.size(); ++i)  
      outer.at(i) = unit_vector_to_spherical_point(unit_vectors[i]);  
  
   return result;  
}  
  
auto operator<<(std::ostream& o, SphericalPolygon const& sp) -> std::ostream& {  
   for (size_t i = 0; i < sp.outer().size(); ++i)  
      o << sp.outer().at(i).x() << ", " << sp.outer().at(i).y() << std::endl;  
  
   return o;  
}  
  
auto main() -> int {  
   auto const nominal_unit_vectors = create_truncated_sphere_unit_vectors(30.0);  
  
   auto accum = unit_vectors_to_spherical_polygon(nominal_unit_vectors);  
  
   auto const decs = 7;  
   for (size_t i = 0; i < decs; ++i) {  
      auto const dec_angle = (i + 1) * 80.0 / decs;  
  
      auto const ticks = 10;  
      for (size_t j = 0; j < ticks; ++j) {  
         auto const angle = j * 360.0 / ticks;  
  
         std::array<double, 3> const axis{cos(rad(angle)), sin(rad(angle)), 0.0};  
  
         auto const poly = unit_vectors_to_spherical_polygon(  
            rotate_ring_about_axis(axis, dec_angle, nominal_unit_vectors));  
  
         std::vector<SphericalPolygon> result;  
  
         auto const p1_is_valid = bg::is_valid(accum);  
         auto const p2_is_valid = bg::is_valid(poly);  
  
         if (!p1_is_valid || !p2_is_valid)  
            throw std::runtime_error("Expected input polygons to be valid");  
  
         bg::union_(accum, poly, result);  
  
         if (result.size() != 1) {  
            std::cout << "Union of two valid polygons failed." << std::endl;  
            return -1;  
         }  
  
         accum = result[0];  
  
         std::cout << bg::area(accum) << std::endl;  
      }  
   }  
  
   return 0;  
}  
```  
  
The output (visual studio 2022) is:  
  
```  
1.04021  
1.11148  
1.18274  
1.25401  
1.32527  
1.39654  
1.46568  
1.51601  
1.54423  
1.5544  
1.71361  
1.80205  
1.89049  
1.97893  
2.06737  
2.15581  
2.24425  
2.33269  
2.40978  
2.43881  
2.58744  
2.68981  
2.79218  
2.89455  
2.99692  
3.09929  
3.20166  
3.30403  
3.4064  
3.46251  
3.60985  
3.72275  
3.83567  
3.94857  
4.06148  
4.17439  
4.2873  
4.40021  
4.51312  
4.59161  
4.73989  
4.85976  
4.97964  
5.09951  
5.21939  
5.33927  
5.45914  
5.57902  
5.6989  
5.79037  
5.93898  
6.06195  
6.18492  
Union of two valid polygons failed.  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2022-11-29 15:01:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1097#issuecomment-1330784543  

Thanks, I can reproduce it in Ubuntu with clang15. If the union of the two spherical polygons is too large (with area larger than the half of the sphere) then boost geometry returns an empty result.   
  
Note that there are two macros that can be useful   
-  `BOOST_GEOMETRY_UNION_THROW_INVALID_OUTPUT_EXCEPTION` that throws an exception for the case of spherical polygons of this type  
- `BOOST_GEOMETRY_UNION_RETURN_INVALID` that can return such an invalid polygon  
  
Maybe it makes sense to throw an exception by default explaining that these large spherical polygons  cannot be handled by the library at this point instead of returning an empty result.
