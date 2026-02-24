# #681 - Adding text to svg_mapper before any geometry causes everything to collapse to a point [Closed]

> Username: sudo-panda  
> Created at: 2020-03-12 10:07:05 UTC  
> Updated at: 2020-04-19 03:12:09 UTC  
> Closed at: 2020-04-19 03:12:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/681  

If this code is run:  
``` C++  
#include <boost/geometry/geometry.hpp>  
#include <fstream>  
  
namespace bg = boost::geometry;  
  
int main()  
{  
    typedef bg::model::d2::point_xy<double> point_t;  
    typedef bg::model::polygon<point_t> polygon_t;  
    polygon_t polygon;  
    point_t point;  
    point = bg::make<point_t>(5.0,5.0);  
    bg::read_wkt("POLYGON((0.27 5.62,0.43 6.02,1.54 7.61,2.82 8.85,8.86 3.83,3.99 2.18,0.27 5.62))", polygon);  
  
    std::ofstream svg("map.svg");  
    bg::svg_mapper<point_t> mapper(svg, 400, 400);  
  
    mapper.text(point_t(5,5), "(5,5)", "fill-opacity:0.5;fill:rgb(0,0,0);",1,1);  
  
    mapper.add(polygon);  
    mapper.add(point);  
    mapper.map(polygon, "fill-opacity:0.5;fill:rgb(100,204,0);stroke:rgb(100,204,0);stroke-width:1px", 1);  
    mapper.map(point, "fill-opacity:0.5;fill:rgb(0,0,0);stroke-width:0px", 2);  
      
}  
```  
the output file ( `map.svg` ) produced is :  
``` svg  
<?xml version="1.0" standalone="no"?>  
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN"  
"http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">  
<svg width="100%" height="100%" version="1.1"  
xmlns="http://www.w3.org/2000/svg"  
xmlns:xlink="http://www.w3.org/1999/xlink">  
<text style="fill-opacity:0.5;fill:rgb(0,0,0);" x="201" y="201">(5,5)</text>  
<g fill-rule="evenodd"><path d="M 200,200 L 200,200 L 200,200 L 200,200 L 200,200 L 200,200 L 200,200 z " style="fill-opacity:0.5;fill:rgb(100,204,0);stroke:rgb(100,204,0);stroke-width:1px"/></g>  
<circle cx="200" cy="200" r="2" style="fill-opacity:0.5;fill:rgb(0,0,0);stroke-width:0px"/>  
</svg>  
```  
  
which shows this:  
![map](https://user-images.githubusercontent.com/16690760/76509457-0f125680-6476-11ea-8f9e-8deeccdf1f1d.png)  
  
while if the same text function is called after the geometries are added:  
``` C++  
#include <boost/geometry/geometry.hpp>  
#include <fstream>  
  
namespace bg = boost::geometry;  
  
int main()  
{  
    typedef bg::model::d2::point_xy<double> point_t;  
    typedef bg::model::polygon<point_t> polygon_t;  
    polygon_t polygon;  
    point_t point;  
    point = bg::make<point_t>(5.0,5.0);  
    bg::read_wkt("POLYGON((0.27 5.62,0.43 6.02,1.54 7.61,2.82 8.85,8.86 3.83,3.99 2.18,0.27 5.62))", polygon);  
  
    std::ofstream svg("map.svg");  
    bg::svg_mapper<point_t> mapper(svg, 400, 400);  
  
    mapper.add(polygon);  
    mapper.add(point);  
    mapper.map(polygon, "fill-opacity:0.5;fill:rgb(100,204,0);stroke:rgb(100,204,0);stroke-width:1px", 1);  
    mapper.map(point, "fill-opacity:0.5;fill:rgb(0,0,0);stroke-width:0px", 2);  
  
    mapper.text(point_t(5,5), "(5,5)", "fill-opacity:0.5;fill:rgb(0,0,0);",1,1);  
}  
```  
  
The output produced is correct:  
``` svg  
<?xml version="1.0" standalone="no"?>  
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN"  
"http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">  
<svg width="100%" height="100%" version="1.1"  
xmlns="http://www.w3.org/2000/svg"  
xmlns:xlink="http://www.w3.org/1999/xlink">  
<g fill-rule="evenodd"><path d="M 4.08562e-14,195.111 L 7.45052,176.484 L 59.1385,102.445 L 118.743,44.7031 L 400,278.463 L 173.225,355.297 L 4.08562e-14,195.111 z " style="fill-opacity:0.5;fill:rgb(100,204,0);stroke:rgb(100,204,0);stroke-width:1px"/></g>  
<circle cx="220.256" cy="223.981" r="2" style="fill-opacity:0.5;fill:rgb(0,0,0);stroke-width:0px"/>  
<text style="fill-opacity:0.5;fill:rgb(0,0,0);" x="221.256" y="224.981">(5,5)</text>  
</svg>  
```  
  
![map(1)](https://user-images.githubusercontent.com/16690760/76509782-8fd15280-6476-11ea-9832-91b2608d93ac.png)  
  
N.B.: Even though the polygon goes out of bounds, it is correct. It's because of the dimensions of the svg provided and not a bug. Although it can be improved so that the polygon is always rendered inside the dimensions. Or a square surrounding the polygon can be added.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2020-04-15 11:48:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/681#issuecomment-613989869  

But you should never do this:  
```  
  mapper.text(point_t(5,5), "(5,5)", "fill-opacity:0.5;fill:rgb(0,0,0);",1,1);  
  mapper.add(polygon);  
```     
  
You can only add texts or map geometries *after* setting the bounding box, using add.  
  
Can we close this issue?
