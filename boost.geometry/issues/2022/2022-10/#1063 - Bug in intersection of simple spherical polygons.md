# #1063 - Bug in intersection of simple spherical polygons [Closed]

> Username: rconde01  
> Created at: 2022-10-08 16:33:32 UTC  
> Updated at: 2022-11-16 13:05:22 UTC  
> Closed at: 2022-11-16 13:05:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/1063  

The following code shows the intersection of two simple spherical polygons. The first is a ring around the z-axis defining a sphere truncated from the bottom. The other is a ring defining a half-sphere. When the latter is rotated about y by 90 degrees, you get a null result where you would expect it to cut the first polygon in half. A 270 degree rotation works as expected, as does a 89 and 91 degree rotation.  
  
I'm not sure, but the problem appears to be in [add_rings.hpp](https://github.com/boostorg/geometry/blob/b18db001b813f486b014744e9dd920e894bc3523/include/boost/geometry/algorithms/detail/overlay/add_rings.hpp#L145) where the area of the result comes out negative and is therefore rejected. Maybe this [comment](https://github.com/boostorg/geometry/blob/b18db001b813f486b014744e9dd920e894bc3523/include/boost/geometry/algorithms/detail/overlay/overlay.hpp#L383) is relevant also.  
  
https://godbolt.org/z/7h5Y9nxev  
  
```  
#include <array>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <cmath>  
#include <iostream>  
#include <vector>  
  
namespace bg = boost::geometry;  
  
using SphericalPoint =  
    bg::model::d2::point_xy<double, bg::cs::spherical_equatorial<bg::degree>>;  
  
using SphericalPolygon =  
    boost::geometry::model::polygon<SphericalPoint,  
                                    false,  // counter-clockwise  
                                    true,   // closed  
                                    std::vector, std::vector, std::allocator,  
                                    std::allocator>;  
  
auto rad(double deg) -> double { return M_PI * deg / 180.0; }  
  
auto deg(double rad) -> double { return 180.0 * rad / M_PI; }  
  
auto create_truncated_sphere_unit_vectors(double cone_half_angle_deg)  
    -> std::vector<std::array<double, 3>> {  
    auto const ring_radius = sin(rad(cone_half_angle_deg));  
    auto const ring_height = cos(rad(cone_half_angle_deg));  
  
    std::vector<std::array<double, 3>> result;  
  
    auto const num_ring_points = 361;  
  
    result.reserve(num_ring_points);  
  
    for (size_t i = 0; i < num_ring_points - 1; ++i) {  
        auto const angle_deg = (360.0 * i) / num_ring_points;  
        result.push_back({ring_radius * cos(rad(angle_deg)),  
                          ring_radius * sin(rad(angle_deg)), ring_height});  
    }  
  
    result.push_back(result.front());  
  
    return result;  
}  
  
auto rotate_about_y(std::array<double, 3> const& vec, double rot_angle_deg)  
    -> std::array<double, 3> {  
    auto const rot_angle_rad = rad(rot_angle_deg);  
  
    auto const sin_angle = sin(rot_angle_rad);  
    auto const cos_angle = cos(rot_angle_rad);  
  
    return {cos_angle * vec[0] + sin_angle * vec[2], vec[1],  
            -sin_angle * vec[0] + cos_angle * vec[2]};  
}  
  
auto rotate_ring_about_y(std::vector<std::array<double, 3>> const& ring,  
                         double rot_angle_deg)  
    -> std::vector<std::array<double, 3>> {  
    std::vector<std::array<double, 3>> result;  
  
    result.reserve(ring.size());  
  
    for (auto const& o : ring)  
        result.push_back(rotate_about_y(o, rot_angle_deg));  
  
    return result;  
}  
  
auto unit_vector_to_spherical_point(std::array<double, 3> const& unit_vector)  
    -> SphericalPoint {  
    auto calculate_theta = [](auto const& vec) {  
        if (vec[0] == 0.0 && vec[1] == 0.0) return 0.0;  
  
        return deg(atan2(vec[1], vec[0]));  
    };  
  
    return SphericalPoint(calculate_theta(unit_vector),  
                          deg(asin(unit_vector[2])));  
}  
  
auto unit_vectors_to_spherical_polygon(  
    std::vector<std::array<double, 3>> const& unit_vectors)  
    -> SphericalPolygon {  
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
    auto const topper_unit_vectors = create_truncated_sphere_unit_vectors(30.0);  
    auto const topper_spherical_polygon =  
        unit_vectors_to_spherical_polygon(topper_unit_vectors);  
  
    auto const half_sphere_unit_vector =  
        create_truncated_sphere_unit_vectors(90.0);  
    auto const half_sphere_plus_90_unit_vector =  
        rotate_ring_about_y(half_sphere_unit_vector, 90.0);  
    auto const half_sphere_plus_270_unit_vector =  
        rotate_ring_about_y(half_sphere_unit_vector, 270.0);  
  
    auto const half_sphere_spherical_polygon =  
        unit_vectors_to_spherical_polygon(half_sphere_unit_vector);  
    auto const half_sphere_plus_90_spherical_polygon =  
        unit_vectors_to_spherical_polygon(half_sphere_plus_90_unit_vector);  
    auto const half_sphere_plus_270_spherical_polygon =  
        unit_vectors_to_spherical_polygon(half_sphere_plus_270_unit_vector);  
  
    // nominal  
    {  
        std::vector<SphericalPolygon> result;  
        bg::intersection(topper_spherical_polygon,  
                         half_sphere_spherical_polygon, result);  
  
        std::cout << "nominal: " << result.size() << std::endl;  
    }  
  
    // plus 90  
    {  
        std::vector<SphericalPolygon> result;  
        bg::intersection(topper_spherical_polygon,  
                         half_sphere_plus_90_spherical_polygon, result);  
  
        std::cout << "plus 90: " << result.size() << std::endl;  
    }  
  
    // plus 270  
    {  
        std::vector<SphericalPolygon> result;  
        bg::intersection(topper_spherical_polygon,  
                         half_sphere_plus_270_spherical_polygon, result);  
  
        std::cout << "plus 270: " << result.size() << std::endl;  
    }  
}  
```

---

## Comment 1

> Username: rconde01  
> Created at: 2022-10-13 17:08:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1063#issuecomment-1277925789  

Ok the issue appears to be in the area calculation. For the 2 datasets below only the first 2 and last points differ by a tiny amount. The first dataset gives the correct answer and the 2nd doesn't. `GeographicLib` gives the same (correct) answer for both.  
  
```  
#include <array>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <cmath>  
#include <iostream>  
#include <vector>  
#include <iostream>  
  
namespace bg = boost::geometry;  
  
using SphericalPoint =  
    bg::model::d2::point_xy<double, bg::cs::spherical_equatorial<bg::degree>>;  
  
using SphericalPolygon =  
    boost::geometry::model::polygon<SphericalPoint,  
                                    true,   // clockwise  
                                    true,   // closed  
                                    std::vector, std::vector, std::allocator,  
                                    std::allocator>;  
  
auto main() -> int {  
  
   // Rotated by 90.0 - 0.00000000000001  
   SphericalPolygon sp_good;  
  
   sp_good.outer().push_back(SphericalPoint(-9.00000000000000142e+01,6.00172345808800074e+01));  
   sp_good.outer().push_back(SphericalPoint(9.00000000000000142e+01,6.00172345808800074e+01));  
   sp_good.outer().push_back(SphericalPoint(8.87671232876712253e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(8.38356164383561691e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(7.89041095890410986e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(7.39726027397260282e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(6.90410958904109719e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(6.41095890410958873e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(5.91780821917808240e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(5.42465753424657606e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(4.93150684931506831e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(4.43835616438356126e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(3.94520547945205493e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(3.45205479452054860e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(2.95890410958904120e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(2.46575342465753415e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(1.97260273972602747e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(1.47945205479452060e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(9.86301369863013733e+00,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(4.93150684931506866e+00,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(0.00000000000000000e+00,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-9.86301369863014976e+00,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-1.47945205479451669e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-1.97260273972602853e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-2.46575342465753522e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-2.95890410958904191e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-3.45205479452054860e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-3.94520547945206062e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-4.43835616438356269e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-4.93150684931507470e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-5.42465753424657606e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-5.91780821917808240e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-6.41095890410958873e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-6.90410958904110146e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-7.39726027397260282e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-7.89041095890410418e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-8.38356164383561691e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-8.87671232876712253e+01,6.00000000000000071e+01));  
   sp_good.outer().push_back(SphericalPoint(-9.00000000000000142e+01,6.00172345808800074e+01));  
  
   std::cout << "good area boost = " << boost::geometry::area(sp_good) << std::endl;  
  
   // Rotated By 90  
   SphericalPolygon sp_bad;  
  
   sp_bad.outer().push_back(SphericalPoint(-9.00000000000000000e+01,6.00172345808800074e+01));  
   sp_bad.outer().push_back(SphericalPoint(9.00000000000000000e+01,6.00172345808800074e+01));  
   sp_bad.outer().push_back(SphericalPoint(8.87671232876712253e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(8.38356164383561691e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(7.89041095890410986e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(7.39726027397260282e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(6.90410958904109719e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(6.41095890410958873e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(5.91780821917808240e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(5.42465753424657606e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(4.93150684931506831e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(4.43835616438356126e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(3.94520547945205493e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(3.45205479452054860e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(2.95890410958904120e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(2.46575342465753415e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(1.97260273972602747e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(1.47945205479452060e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(9.86301369863013733e+00,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(4.93150684931506866e+00,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(0.00000000000000000e+00,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-9.86301369863014976e+00,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-1.47945205479451669e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-1.97260273972602853e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-2.46575342465753522e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-2.95890410958904191e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-3.45205479452054860e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-3.94520547945206062e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-4.43835616438356269e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-4.93150684931507470e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-5.42465753424657606e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-5.91780821917808240e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-6.41095890410958873e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-6.90410958904110146e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-7.39726027397260282e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-7.89041095890410418e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-8.38356164383561691e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-8.87671232876712253e+01,6.00000000000000071e+01));  
   sp_bad.outer().push_back(SphericalPoint(-9.00000000000000000e+01,6.00172345808800074e+01));  
  
   std::cout << "bad area boost = " << boost::geometry::area(sp_bad) << std::endl;  
  
}  
```  
  
```  
good area boost = 0.420407  
bad area boost = -5.86278  
```

---

## Comment 2

> Username: rconde01  
> Created at: 2022-10-13 19:51:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1063#issuecomment-1278108295  

Here's a workaround which substitutes the `GeographicLib` area calculation:  
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <GeographicLib/PolygonArea.hpp>  
  
// refer to boost/geometry/strategy/spherical/area.hpp  
class MySphericalAreaStrategy {  
   public:  
    template <typename Geometry>  
    struct result_type  
        : boost::geometry::strategy::area::detail::result_type<Geometry, void> {  
    };  
  
    template <typename Geometry>  
    class state {  
       public:  
        state() : m_geod(1.0, 0.0), m_polygon_area(m_geod) {}  
  
       private:  
        friend class MySphericalAreaStrategy;  
  
        GeographicLib::Geodesic m_geod;  
        GeographicLib::PolygonArea m_polygon_area;  
    };  
  
    template <typename PointOfSegment, typename Geometry>  
    inline void apply(PointOfSegment const& p1, PointOfSegment const& p2,  
                      state<Geometry>& st) const {  
        st.m_polygon_area.AddPoint(p1.y(), p1.x());  
    }  
  
    template <typename Geometry>  
    inline typename result_type<Geometry>::type result(  
        state<Geometry> const& st) const {  
        double perimeter, area;  
  
        st.m_polygon_area.Compute(true, true, perimeter, area);  
  
        return area;  
    }  
};  
  
// refer to boost/geometry/strategies/relate/spherical.hpp  
class MyIntersectionStrategies  
    : public boost::geometry::strategies::relate::spherical<> {  
   public:  
    template <typename Geometry>  
    auto area(  
        Geometry const&,  
        std::enable_if_t<!boost::geometry::util::is_box<Geometry>::value>* =  
            nullptr) const {  
        return MySphericalAreaStrategy();  
    }  
};  
  
.  
.  
.  
  
MyIntersectionStrategies strategy;  
boost::geometry::intersection(poly1,poly2,result,strategy);  
  
```

---

## Comment 3

> Username: vissarion  
> Created at: 2022-10-21 14:45:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1063#issuecomment-1287066186  

Thanks for reporting this. I have created a PR https://github.com/boostorg/geometry/pull/1074 that fixes this issue.   
The area is indeed in area computations for polygons with spherical segments that pass through the pole.

---

## Comment 4

> Username: rconde01  
> Created at: 2022-10-21 14:54:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/1063#issuecomment-1287078118  

Great!
