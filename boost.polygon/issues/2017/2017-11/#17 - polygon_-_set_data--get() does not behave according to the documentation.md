# #17 - polygon_*_set_data::get() does not behave according to the documentation [Open]

> Username: ton  
> Created at: 2017-11-17 12:35:02 UTC  
> Updated at: 2017-11-22 14:53:25 UTC  
> Url: https://github.com/boostorg/polygon/issues/17  

According to the documentation for the `get()` method on both `polygon_45_set_data` and `polygon_90_set_data`, output polygons should always have counter-clockwise winding, and no duplicated start and end point:  
  
> Polygons will be output with counterclockwise winding, hole polygons will be output with clockwise winding.  The last vertex of an output polygon is not the duplicate of the first, and the number of points is equal to the number of edges.  
  
The following code demonstrates an issue with `get()`, where in case of `polygon_45_set_data::get`, the start- and end vertex is duplicated, and in case of `polygon_90_set_data::get`, the winding is incorrect:  
  
```  
#include <boost/polygon/polygon.hpp>  
#include <boost/polygon/polygon_90_set_data.hpp>  
  
namespace bp = boost::polygon;  
  
int main()  
{  
  using Polygon45 = bp::polygon_45_data<int>;  
  using Polygon90 = bp::polygon_90_data<int>;  
  using Point = bp::point_data<int>;  
  using Polygon45Set = bp::polygon_45_set_data<int>;  
  using Polygon90Set = bp::polygon_90_set_data<int>;  
  
  using namespace boost::polygon::operators;  
  
  {  
    Polygon90 A;  
    {  
      std::vector<Point> points{{0, 0}, {1, 0}, {1, 1}, {0, 1}};  
      bp::set_points(A, points.begin(), points.end());  
    }  
  
    Polygon90 B;  
    {  
      std::vector<Point> points{{2, 0}, {3, 0}, {3, 1}, {2, 1}};  
      bp::set_points(B, points.begin(), points.end());  
    }  
  
    std::vector<Polygon90> result;  
    Polygon90Set(A - B).get<std::vector<Polygon90>>(result);  
  
    std::cout << "Non counter-clockwise winding:\n";  
    for (const auto& polygon : result)  
    {  
      for (const Point& p : polygon)  
      {  
        std::cout << '(' << std::to_string(p.x()) << ", " << std::to_string(p.y()) << ")\n";  
      }  
    }  
  }  
  
  {  
    Polygon45 A;  
    {  
      std::vector<Point> points{{0, 0}, {1, 0}, {1, 1}};  
      bp::set_points(A, points.begin(), points.end());  
    }  
  
    Polygon45 B;  
    {  
      std::vector<Point> points{{2, 0}, {3, 0}, {3, 1}};  
      bp::set_points(B, points.begin(), points.end());  
    }  
  
    std::vector<Polygon45> result;  
    Polygon45Set(A - B).get<std::vector<Polygon45>>(result);  
  
    std::cout << "Duplicated start- and end vertex:\n";  
    for (const auto& polygon : result)  
    {  
      for (const Point& p : polygon)  
      {  
        std::cout << '(' << std::to_string(p.x()) << ", " << std::to_string(p.y()) << ")\n";  
      }  
    }  
  }  
  
  return 0;  
}  
```  
Output:  
```  
Non counter-clockwise winding:  
(1, 0)  
(0, 0)  
(0, 1)  
(1, 1)  
Duplicated start- and end vertex:  
(1, 1)  
(0, 0)  
(1, 0)  
(1, 1)  
```  
Is this in fact an issue in the code (not the documentation)?

---

## Comment 1

> Username: asydorchuk  
> Created at: 2017-11-20 15:57:15 UTC  
> Url: https://github.com/boostorg/polygon/issues/17#issuecomment-345739050  

Hi Ton, I was not present during initial development of the library, so not sure what was the actual intent. However, you are right, that the implementation is inconsistent with documentation.

---

## Comment 2

> Username: ton  
> Created at: 2017-11-20 16:46:50 UTC  
> Updated at: 2017-11-20 16:47:53 UTC  
> Url: https://github.com/boostorg/polygon/issues/17#issuecomment-345755312  

Hi asydorchuk, thanks for the feedback. Do you know whether this is something that used to work? I can investigate this a bit further once I come around to it, but it will take some time since I am completely unfamiliar with the code.  
  
In any case, it is not a critical issue, since it is pretty easy to workaround, at the cost of some runtime performance. So in case this can be more efficiently handled on the Boost Polygon side, that would be great.

---

## Comment 3

> Username: asydorchuk  
> Created at: 2017-11-22 14:02:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/17#issuecomment-346358504  

Hi Ton, it's a bit hard to say what is the right thing to do in this situation. Since updating the code will make it backward incompatible for people who already built workarounds on their side. Perhaps the best solution is to update the documentation according to the current behaviour. What do you say?

---

## Comment 4

> Username: ton  
> Created at: 2017-11-22 14:52:50 UTC  
> Updated at: 2017-11-22 14:53:25 UTC  
> Url: https://github.com/boostorg/polygon/issues/17#issuecomment-346372647  

Hey asydorchuk, I agree changing behavior might be unwanted for existing users, so then only changing the documentation is required indeed.
