# #1374 - distance result error [Closed]

> Username: Jimyzzp  
> Created at: 2025-02-12 08:05:26 UTC  
> Updated at: 2025-02-14 13:38:39 UTC  
> Closed at: 2025-02-14 13:38:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1374  

```cpp  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/io/svg/write_svg.hpp>  
  
namespace bg = boost::geometry;  
  
typedef bg::model::point<double, 2, bg::cs::cartesian> point_t;  
typedef bg::model::linestring<point_t> linestring_t;  
typedef bg::model::polygon<point_t> polygon_t;  
  
int main(){  
	polygon_t pg1;  
	bg::append(pg1.outer(), point_t{1545, 4435});  
	bg::append(pg1.outer(), point_t{1545, 4540});  
	bg::append(pg1.outer(), point_t{1405, 4540});  
	bg::append(pg1.outer(), point_t{1405, 4435});  
	linestring_t l2{  
		point_t{1435, 4725},  
		point_t{1450, 4710},  
	};  
	double d = bg::distance(pg1, l2); // why the result of d is zero?  
    return 0;  
}  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2025-02-14 13:38:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1374#issuecomment-2659363191  

The polygon is invalid because it is declared as closed (and it is not) and as clockwise (and it is not).   
If you append the first point at the end and you also reverse the order to be clockwise you will get the correct distance which is 170.   
  
Equivalently, you can just define your polygon as not closed and not clockwise  
`typedef bg::model::polygon<point_t, false, false> polygon_t;`  
  
For more details see the [documentation](https://www.boost.org/doc/libs/1_87_0/libs/geometry/doc/html/geometry/reference/models/model_polygon.html)
