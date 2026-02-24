# #799 - bg::area underflow with geographic coordinates [Closed]

> Username: CJxD  
> Created at: 2021-01-21 21:33:38 UTC  
> Updated at: 2021-06-03 14:45:42 UTC  
> Closed at: 2021-03-02 14:50:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/799  

`bg::area()` with a geographic polygon yields unexpected results when the area of the polygon is less than ~20km² when using `double` for the `point` precision.  
  
In this example, a 3m radius octagon is created with `double` precision points. The expected area is about 26m², but instead we get -12,375m².  
  
This goes against expectations because `double` is able to define GPS coordinates to around 1mm of precision.  
  
When `precision_t` is set to `long double`, this example works. However, `long double` is not available on some architectures, and I discovered this bug after strange behaviour when running the code on both arm32 and arm64 smartphones.  
  
My best guess is that there is an avoidable numerical underflow somewhere in `boost/geometry/formulas/area_formulas.hpp:418-547`.  
  
```  
#include <cmath>  
#include <iostream>  
  
#include <boost/geometry/geometry.hpp>  
  
#define EARTH_CIRCUMFERENCE 40075017  
#define DEG_LAT_METRES 111320  
  
namespace bg = boost::geometry;  
  
using precision_t = double; // Works with `long double`, which not available on some architectures (notably AArch64)  
using point_t = bg::model::point<precision_t, 2, bg::cs::geographic<bg::degree>>;  
using polygon_t = bg::model::polygon<point_t, false, true>;  
  
template <typename T, std::enable_if_t<std::is_floating_point<T>::value, void*> = nullptr>  
inline T deg2rad(T degrees) {  
    return degrees * static_cast<T>(M_PI) / static_cast<T>(180);  
}  
  
template <typename T, std::enable_if_t<std::is_floating_point<T>::value, void*> = nullptr>  
inline T metresToDegreesLatitude(T metres) {  
    return metres / static_cast<T>(DEG_LAT_METRES);  
}  
  
template <typename T, std::enable_if_t<std::is_floating_point<T>::value, void*> = nullptr>  
inline T metresToDegreesLongitude(T metres, T latitude) {  
    return metres * static_cast<T>(360) /  
        (static_cast<T>(EARTH_CIRCUMFERENCE) * std::cos(deg2rad(latitude)));  
}  
  
int main() {  
    precision_t la = 51.47;  
    precision_t lo = -0.02;  
    precision_t radius = 3.0;  
  
    precision_t dLat = metresToDegreesLatitude(radius);  
    precision_t dLon = metresToDegreesLongitude(radius, la);  
    constexpr precision_t invrt2 = M_SQRT1_2;  
  
    polygon_t poly {  
            {  
                { lo, la + dLat },  
                { lo - dLon * invrt2, la + dLat * invrt2 },  
                { lo - dLon, la },  
                { lo - dLon * invrt2, la - dLat * invrt2 },  
                { lo, la - dLat },  
                { lo + dLon * invrt2, la - dLat * invrt2 },  
                { lo + dLon, la },  
                { lo + dLon * invrt2, la + dLat * invrt2 },  
                { lo, la + dLat }  
            }  
    };  
  
    precision_t n = poly.outer().size();  
    precision_t theoreticalArea = n * radius * radius * std::sin(2.0 * M_PI / n) / 2.0;  
  
    std::cout << "Polygon: " << std::fixed << bg::wkt(poly) << std::endl;  
    std::cout << "Is valid: " << std::boolalpha << bg::is_valid(poly) << std::endl;  
    std::cout << "Expected area: " << theoreticalArea << std::endl;  
    std::cout << "Calculated area: " << bg::area(poly) << std::endl;  
}  
```  
  
Output:  
```  
Polygon: POLYGON((-0.020000 51.470027,-0.020031 51.470019,-0.020043 51.470000,-0.020031 51.469981,-0.020000 51.469973,-0.019969 51.469981,-0.019957 51.470000,-0.019969 51.470019,-0.020000 51.470027))  
Is valid: true  
Expected area: 26.032898  
Calculated area: -12375.450647  
```

---

## Comment 1

> Username: CJxD  
> Created at: 2021-01-21 23:07:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/799#issuecomment-764998647  

Related: https://stackoverflow.com/questions/65493910/boost-point-circle-coming-out-in-weird-shapes/65496105?noredirect=1#comment115801317_65496105

---

## Comment 2

> Username: vissarion  
> Created at: 2021-02-01 15:12:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/799#issuecomment-770928173  

Thanks for filing this issue. https://github.com/boostorg/geometry/pull/801 proposes a fix that will improve the accuracy of your example and maybe more cases. The reason of inaccuracy was multifold. This also improves most of the cases in https://stackoverflow.com/questions/65493910/boost-point-circle-coming-out-in-weird-shapes/65496105?noredirect=1#comment115801317_65496105 but there is an extra issue on the geographic buffer.
