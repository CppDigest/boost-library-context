# #510 - boost::geometry::intersection produces wrong result [Open]

> Username: olologin  
> Created at: 2018-09-21 11:09:06 UTC  
> Updated at: 2018-10-09 20:27:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/510  

Hi everyone. I've just noticed unit test which produced correct result on boost 1.55, but it fails on 1.67  
Is that known? No matter if clippingRegion is defined in CW or CCW direction, it always gives me 2 lines as a result of intersection. But with the same input old boost gave me 1 (but very small) correct subsegment as a result.  
  
Could you please help with that?  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/multi/geometries/multi_polygon.hpp>  
using namespace boost::geometry::model;  
using namespace boost::geometry;  
  
typedef d2::point_xy<double>				BoostPoint;  
typedef polygon<BoostPoint >				BoostPolygon;  
typedef linestring<BoostPoint>				BoostPolyline;  
  
int main(void)  
{  
	BoostPolyline polyToClip;  
	std::vector<BoostPolyline> clippingResult;  
  
	polyToClip.push_back(BoostPoint(-7.7866132709658586e-005, 6.0810736257569502));  
	polyToClip.push_back(BoostPoint(-7.7866132709658586e-005, -6.0811651098339610));  
  
  
	multi_polygon<BoostPolygon> clippingRegion;  
	BoostPolygon curPolygon;  
	append(curPolygon, BoostPoint(0.084821928797522289, 1.9999244955432876));  
	append(curPolygon, BoostPoint(1.8813618198054716e-016, 2.0001895226089994));  
	append(curPolygon, BoostPoint(-7.7866133709978802e-005, 1.9997988605928652));  
	append(curPolygon, BoostPoint(0.00000000000000000, 1.9994081985767305));  
	append(curPolygon, BoostPoint(0.00000000000000000, -2.0001895226089998));  
	append(curPolygon, BoostPoint(0.084821928797522289, 1.9999244955432876));  
	clippingRegion.push_back(curPolygon);  
	reverse(clippingRegion);  
  
	intersection(clippingRegion, polyToClip, clippingResult);  
	return 0;  
}  
```  
  
Here you can see how polyToClip (red and pink) gets separated by point 2 of clippingRegion (green). But even If I change direction of clippingRegion - it still produces the same result.  
  
![image](https://user-images.githubusercontent.com/4762581/45877743-8d24fe00-bda7-11e8-9428-d40bb9e2d640.png)

---

## Comment 1

> Username: olologin  
> Created at: 2018-09-22 06:17:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/510#issuecomment-423720259  

Same result is produced by boost::geometry::intersection from 1.68

---

## Comment 2

> Username: DaveBy  
> Created at: 2018-10-09 20:27:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/510#issuecomment-428339152  

I also have wrong intersection result. Polygons: "MULTIPOLYGON( ( (-2 -2,-2 2,2 2,2 -2) ) )" and "MULTIPOLYGON( ( (-20 -1,-2 1,2 2,2 -1) ) )". boost version is 1.68
