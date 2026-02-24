# #498 - Unexpected results with expand() and make_inverse() for spherical and geographic CS [Closed]

> Username: vissarion  
> Created at: 2018-07-02 10:29:42 UTC  
> Updated at: 2018-07-10 11:43:17 UTC  
> Closed at: 2018-07-10 08:23:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/498  

The code below (as posted in boost.geometry mailing list) returns   
  
```  
((0, 0), (5, 5))  
((0, 0), (5, 5))  
((-180, 0), (180, 5))  
((-3.14159, 0), (3.14159, 5))  
```  
  
which is unexpected. There is an issue in the initialization of the box using `make_inverse`. Note that if the box is initialized by the first point `point_type{0,0}` of the expansion the result is as expected.   
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/algorithms/expand.hpp>  
#include <boost/geometry/core/cs.hpp>  
#include <boost/geometry/geometries/box.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
#include <iostream>  
  
// XXX -- default template parameters correspond to the Boost.Geometry  
//        example; see  
// https://www.boost.org/doc/libs/1_67_0/libs/geometry/doc/html/geometry/reference/algorithms/expand/expand_2.html  
//  
template < typename Coordinate = short int  
           , typename CS = boost::geometry::cs::cartesian  
           >  
struct expand_example  
{  
  static void apply ()  
  {  
    using point_type = boost::geometry::model::d2::point_xy<Coordinate,CS>;  
    using box_type = boost::geometry::model::box<point_type>;  
  
    using boost::geometry::expand;  
  
    box_type box = boost::geometry::make_inverse<box_type>();  
  
    expand(box, point_type{0,0});  
    expand(box, point_type{1,2});  
    expand(box, point_type{5,4});  
    expand(box, boost::geometry::make<box_type>(3,3,5,5));  
  
    std::cout << boost::geometry::dsv(box) << std::endl;  
  }  
};  
  
int main ()  
{  
  using namespace boost::geometry;  
  expand_example<>::apply();  
  expand_example<double,cs::cartesian>::apply();  
  expand_example<double,cs::geographic<degree>>::apply();  
  expand_example<double,cs::geographic<radian>>::apply();  
}```
