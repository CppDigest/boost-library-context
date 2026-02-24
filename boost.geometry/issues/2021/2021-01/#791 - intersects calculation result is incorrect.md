# #791 - intersects calculation result is incorrect [Closed]

> Username: sotex  
> Created at: 2021-01-04 08:27:46 UTC  
> Updated at: 2021-01-12 09:39:24 UTC  
> Closed at: 2021-01-12 02:28:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/791  

I tested the intersection calculation, and the results don't seem to be correct.  
  
**The test code is as follows:**  
  
[Wandbox](https://wandbox.org/permlink/H7AZMx3Sx2dyViSZ)  
  
```cpp  
#include <iostream>  
#include <iomanip>  
  
#include <boost/geometry.hpp>  
  
typedef boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian> PointT;  
typedef boost::geometry::model::polygon<PointT> PolygonT;  
  
int main()  
{  
    PolygonT poly, box;  
  
    boost::geometry::read_wkt("POLYGON ((112.5323 35.6394, 111.7749 31.7842, 115.6311 31.3255, 116.8506 34.8228,112.5323 35.6394))", poly);  
     
    boost::geometry::read_wkt("POLYGON ((115.07 35.16,115.071 35.16,115.071 35.161,115.07 35.161,115.07 35.16))", box);  
  
    bool b = boost::geometry::intersects(poly, box);  
  
    std::cout << "Intersects: " << (b ? "YES" : "NO") << std::endl;  
  
    std::cout << std::setprecision(15);  
    std::cout << boost::geometry::wkt(box) << std::endl;  
    std::cout << boost::geometry::wkt(poly) << std::endl;  
  
    return 0;  
}  
```  
  
**outputs:**  
  
```bash  
Intersects: NO  
POLYGON((115.07 35.16,115.071 35.16,115.071 35.161,115.07 35.161,115.07 35.16))  
POLYGON((112.5323 35.6394,111.7749 31.7842,115.6311 31.3255,116.8506 34.8228,112.5323 35.6394))  
```  
  
These two geometry should intersect.  
  
```json  
{  
  "type": "FeatureCollection",  
  "features": [  
    {  
      "type": "Feature",  
      "properties": {},  
      "geometry": {  
        "type": "Polygon",  
        "coordinates": [[[112.5323,35.6394],[111.7749,31.7842],[115.6311,31.3255],[116.8506,34.8228],[112.5323,35.6394]]]  
      }  
    },  
    {  
      "type": "Feature",  
      "properties": {},  
      "geometry": {  
        "type": "Polygon",  
        "coordinates": [[[115.07,35.16],[115.07,35.161],[115.071,35.161],[115.071,35.16],[115.07,35.16]]]  
      }  
    }  
  ]  
}  
```  
![image](https://user-images.githubusercontent.com/16911453/103515043-e1851680-4ea8-11eb-8920-93f2a2f098c9.png)

---

## Comment 1

> Username: vissarion  
> Created at: 2021-01-11 12:09:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/791#issuecomment-757911473  

It seems that this is an inaccuracy in the drawing/display environment.   
  
The following piece of code computes the intersection points of the polygon with segments that support the vertical segments of the box but are longer (to guarantee intersection). Then, we can compute the determinant value to see that those points are very close to the tested polygon segment while the points of the box are clearly all from one side of the segment and thus there is no intersection.  
  
```  
#include <iostream>  
#include <iomanip>  
  
#include <boost/geometry.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
using mp = boost::multiprecision::cpp_bin_float_100;  
  
typedef boost::geometry::model::point<mp, 2, boost::geometry::cs::cartesian> PointT;  
typedef boost::geometry::model::polygon<PointT> PolygonT;  
typedef boost::geometry::model::linestring<PointT> LinestringT;  
  
template  
<  
    typename P1,  
    typename P2,  
    typename P  
>  
static inline  
auto side_value(P1 const& p1, P2 const& p2, P const& p)  
{  
    auto const x = boost::geometry::get<0>(p);  
    auto const y = boost::geometry::get<1>(p);  
  
    auto const sx1 = boost::geometry::get<0>(p1);  
    auto const sy1 = boost::geometry::get<1>(p1);  
    auto const sx2 = boost::geometry::get<0>(p2);  
    auto const sy2 = boost::geometry::get<1>(p2);  
  
    auto const dx = sx2 - sx1;  
    auto const dy = sy2 - sy1;  
    auto const dpx = x - sx1;  
    auto const dpy = y - sy1;  
  
    return boost::geometry::detail::determinant<mp>  
            (  
                dx, dy,  
                dpx, dpy  
            );  
}  
  
int main()  
{  
    PolygonT poly, box;  
  
    boost::geometry::read_wkt("POLYGON ((112.5323 35.6394, 111.7749 31.7842, 115.6311 31.3255, 116.8506 34.8228,112.5323 35.6394))", poly);  
  
    boost::geometry::read_wkt("POLYGON ((115.07 35.16,115.071 35.16,115.071 35.161,115.07 35.161,115.07 35.16))", box);  
  
    LinestringT l_poly, l1, l2;  
  
    boost::geometry::read_wkt("LINESTRING (115.07 35.10,115.07 35.20)", l1);  
    boost::geometry::read_wkt("LINESTRING (115.071 35.10,115.071 35.20)", l2);  
    boost::geometry::read_wkt("LINESTRING (116.8506 34.8228,112.5323 35.6394)", l_poly);  
  
  
    bool b = boost::geometry::intersects(poly, box);  
  
    std::cout << std::setprecision(15);  
    std::cout << boost::geometry::wkt(poly) << std::endl;  
    std::cout << boost::geometry::wkt(box) << std::endl;  
  
    std::vector<PointT> outp;  
    boost::geometry::intersection(l_poly, l1, outp);  
    std::cout << boost::geometry::wkt(outp[0]) << std::endl;  
  
    boost::geometry::intersection(l_poly, l2, outp);  
    std::cout << boost::geometry::wkt(outp[1]) << std::endl;  
  
    std::cout << "Intersects: " << (b ? "YES" : "NO") << std::endl;  
  
    std::cout << side_value(poly.outer()[3], poly.outer()[4], box.outer()[0]) << std::endl;  
    std::cout << side_value(poly.outer()[3], poly.outer()[4], box.outer()[1]) << std::endl;  
    std::cout << side_value(poly.outer()[3], poly.outer()[4], box.outer()[2]) << std::endl;  
    std::cout << side_value(poly.outer()[3], poly.outer()[4], box.outer()[3]) << std::endl;  
    std::cout << side_value(poly.outer()[3], poly.outer()[4], outp[0]) << std::endl;  
    std::cout << side_value(poly.outer()[3], poly.outer()[4], outp[1]) << std::endl;  
  
  
    return 0;  
}  
```  
  
returns  
  
```POLYGON((112.5323 35.6394,111.7749 31.7842,115.6311 31.3255,116.8506 34.8228,112.5323 35.6394))  
POLYGON((115.07 35.16,115.071 35.16,115.071 35.161,115.07 35.161,115.07 35.16))  
POINT(115.07 35.1595153648426)  
POINT(115.071 35.1593262626497)  
Intersects: NO  
-0.0020928  
-0.0029094  
-0.0072277  
-0.0064111  
-3.88615713035973e-99  
1.14298739128227e-99  
```

---

## Comment 2

> Username: sotex  
> Created at: 2021-01-12 02:27:10 UTC  
> Updated at: 2021-01-12 02:44:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/791#issuecomment-758352009  

@vissarion Thank you for your answer.  
  
> It seems that this is an inaccuracy in the drawing/display environment.  
  
You're right, it's really the drawing/display problem,I used [geojson.io](geojson.io) to display before.  
  
I redrew it with a `geogebar`, and it's true that they don't intersect.  
  
![geogebar](https://user-images.githubusercontent.com/16911453/104261592-d772a180-54c0-11eb-96be-78de2a54981d.png)
