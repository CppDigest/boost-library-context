# #916 - Intersection of geographic segment that crosses the antimeridian with its envelope [Open]

> Username: anddmb  
> Created at: 2021-10-06 12:53:32 UTC  
> Updated at: 2022-11-22 14:39:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/916  

Dear maintainers,  
  
with boost 1.77, the following program  
  
``` c++  
#include <boost/geometry.hpp>  
  
#include <iostream>  
  
namespace bg = boost::geometry;  
namespace bm = bg::model::d2;  
  
using point = bm::point_xy<double, bg::cs::geographic<bg::degree> >;  
using linestring = bg::model::linestring<point>;  
using multi_linestring = bg::model::multi_linestring<linestring>;  
using box = bg::model::box<point>;  
  
point a() { return { -179.9958, -47.2835 }; }  
point b() { return {  179.9972, -47.2753 }; }  
  
linestring ls() {return {a(), { b() }};}  
  
int main() {  
    multi_linestring result;  
    auto const envelope = bg::return_envelope<box>(ls());  
    bool const success = bg::intersection (envelope, ls(), result);  
    std::cout << "input    : " << bg::wkt(ls()) << std::endl;  
    std::cout << "envelope : " << bg::wkt(envelope) << std::endl;  
    std::cout << "success  : " << success << std::endl;  
    std::cout << "output   : " << bg::wkt(result) << std::endl;  
    return 0;  
}  
  
```  
yields the output (<a href="https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCBmABykAA6oCoRODB7evnrJqY4CQSHhLFEx8baY9vkMQgRMxASZPn5cFVXptfUEhWGR0bEJCnUNTdmtQ109xaUDAJS2qF7EyOwc5gDMwcjeWADUJutuEagpBAD0wJiobATEAJ4AdAiJiQfYJhoAgh/fZpsM2y8ewObicQ2ImFYbx%2BP2YbAUiSYK12EWA%2B3WABEUSchiAQJdrphbncDlYvnDMAikZgUSx0VjUXiWBhKnj0GZSTCvl5UkZdslggR6bS8QLBAB9VQkw7oRYReikFGgFAKPEE4DEJiJBB4UyHRkgLAazCYN7o97rMmfHnBNG0YKU2624UG5lYWh4%2B0hcG2kFigjQy0/G18lheWiOcVex3EZ0HBnKt2skBhiN4KMOn1GEHRrPAQNWkNo46qF2JlkekAlv2oQUFrmff27JgQWb7ADsFl2EIISwYHa7AFouO2AJwPUejgCsCV2g8k7Ye8XWU47GNJa5%2BTYirYH3aJfb3uxH48n7bMivni7M7an603ls3Xx%2BuadfNoCl3Jk7PcP34sLbzHuO5tt%2B67tuuQYQQ2gq7CwTDBF%2BnY/LsqFweGkavrGfIQgoGGcl8aHNl4RC7GgDBDLsjAAG6VKgiQ0vGSp4r%2BxAMOKNF0QxILVus2AQB%2BrazARnxEccnhkQIlF4cgKwKAoZZ4oK0RKA46S7BAnG0PRmCKoJQG4RhwlBoRaFDOgeJoCR6JuCC%2BxmGYwSJNZaEgPZZg2XZBoAO4ANYEAJn6zKBhx2eZeKuLQIlEeFKCLEKIJ2eYZhaTpuxuclnmHMxIB%2BQFqUMSFtnZbFkXRWZBAWXF1mJdlyUyXJCnpe5WVuLsDWUgptVtaVDDoFFJmiRVVVWQloV1Q58XOUKqEZQ5rU5XlECGRGRVhZVEV9QNVpEax/YaCJYE/Bw8wehwU68H4HBaKQqCcLZljWO1izLIxfw8KQBCaCd8y%2BSAkhmA8GgnpIcQzmYkijusGijvonCSJd323ZwvCqhon3ffMcCwEgaAsIkdDROQlB4wT9AxMAXBcBeNARiplAREjETBPUdycB9eNsIIADyDC0Gz128Fg8FGOIgukPgEJqbRqri5gqiYMgJGrDdylnTd9oRJq9weFgSNOiw7MnXwBjAAoABqeCYN53MMVdH38IIIhiOwUgyIIigqOo4u6K0BhGCg1jWPoeARKqsAUlWOIEKQtExNw7btj9t2JNUsuo5UivVC4fWjC0pCBA60z9K0uRpAIec5Ck5cMFMfQxOMmdqQInQjJ4zR6HYWcdMM3RF/Xne95X4y93XJQl/MCgvSsei3CaRunZwF2kFdN13RwqhxAAbIOW%2BSLswCyceXBLhpD1WJYiq4IQJD2esrS7B4%2BOE8Qd9cLMvBfYLwWkH9DkPFOamVMHJcDiKODQe9pBnQRivJG69UYgHRl/LQWMYCIBAFNEixMICkxfv4fARAG7u2EKIcQbtHbyCUGofWEJVgfW8pqRIC84bnURuLde3MSLTV2KgKguxN47z3gfI%2BVNT4QCfmTaIb8P4Y2/r9EAK4HiSC3pOdYo4uCjm3hoDQoMWEwNXrweBthEGyJQYvDgZg2FrxRqY5OcdUjOEkEAA%3D%3D%3D">Compiler Explorer</a>)  
  
```  
input    : LINESTRING(-179.996 -47.2835,179.997 -47.2753)  
envelope : POLYGON((179.997 -47.2835,179.997 -47.2753,180.004 -47.2753,180.004 -47.2835,179.997 -47.2835))  
success  : 1  
output   : MULTILINESTRING((179.997 -47.2753))  
```  
  
The envelope appears to be correct. However, the intersection of the segment with its own envelope only yields a single point.  
  
In the case of the intersection, it seems that boost::geometry assumes that the geodesic between `a()` and `b()` does not cross the antimeridian.  
  
While this appears to be a bug, is there any simple workaround other than rotating back/forth before/after the transformation?  
  
Thanks a lot in advance! 😄   
  
Kind regards,  
Andreas  
  
<sup>  
Andreas Dirks <a href="mailto:andreas.dirks@daimler.com">andreas.dirks@daimler.com</a> on behalf of MBition GmbH <a href="https://github.com/Daimler/daimler-foss/blob/master/PROVIDER_INFORMATION.md">Provider Information</a>  
</sup>

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-10-06 16:33:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/916#issuecomment-936636271  

Thanks for the report!  
  
Yes, this is a bug but it's more severe than you suggested. For set operations of linear geometry vs box the use of cartesian strategy is hardcoded:  
https://github.com/boostorg/geometry/blob/6b74f7c8a344495384cc5ac4f74bb7a307b7c1d1/include/boost/geometry/algorithms/detail/overlay/intersection_insert.hpp#L682-L685  
https://github.com/boostorg/geometry/blob/6b74f7c8a344495384cc5ac4f74bb7a307b7c1d1/include/boost/geometry/algorithms/detail/overlay/clip_linestring.hpp#L39-L51  
AFAIS this set operation was always implemented like that.  
  
Since the operation performs cartesian computation it finds the intersection between a cartesian segment `(-179.996 -47.2835,179.997 -47.2753)` and cartesian box `(179.997 -47.2835, 180.004 -47.2753)` which is indeed a single point.  
  
![obraz](https://user-images.githubusercontent.com/1226951/136243676-0a7af4c0-4c8b-49b0-a1ec-6a7a0514faa1.png)  
  
The fix would be the implementation of correct strategies for spherical and geographic coordinate systems.  
  
As for the workaround. Now that you know that the operation is in fact cartesian you could account for that by adapting data and performing the operation several times. Another possibility would be the conversion of the box to a polygon. However then its shape would be different because in Boost.Geometry edges of boxes are defined by meridians and parallels but edges of polygons are defined by geodesics. Or maybe yet another approach would be better in your case. It depends what would you like to do with it?
