# #1085 - Compilation error while using Buffer in 1.80 [Closed]

> Username: ntalele  
> Created at: 2022-11-08 01:03:12 UTC  
> Updated at: 2022-12-07 17:50:58 UTC  
> Closed at: 2022-12-07 08:30:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085  

Hi,   
I have been using boost::geometry::buffer with the the following input and output types  
Geometry In = Geometry registered as ring_tag. Let's call this GeometryRingType.  
Geometry Out = `std::vector<GeometryRingType>`  
  
This has been working correctly until boost 1.75. But recently when I tried to upgrade to 1.80 it gave me a compilation failure raising a "Not implemented for this type" error for output of type `std::vector<GeometryRingType>`.   
Any help would be appreciated!

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-11-08 08:42:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085#issuecomment-1306838101  

Hi, that does not sound familiar to me. It should not have been changed, as far as I know.  
Do you have small but complete reproduction code for me?

---

## Comment 2

> Username: ntalele  
> Created at: 2022-11-09 21:56:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085#issuecomment-1309428649  

@barendgehrels Sure! Here is a small code snippet  
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
int main()  
{  
    typedef double coordinate_type;  
    typedef boost::geometry::model::d2::point_xy<coordinate_type> point;  
    // typedef boost::geometry::model::polygon<point> polygon;  
  
    // Declare strategies  
    const double buffer_distance = 1.0;  
    const int points_per_circle = 36;  
    boost::geometry::strategy::buffer::distance_symmetric<coordinate_type> distance_strategy(buffer_distance);  
    boost::geometry::strategy::buffer::join_round join_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::end_round end_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::point_circle circle_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::side_straight side_strategy;  
  
    // Declare output  
    // boost::geometry::model::multi_polygon<polygon> result;  
    std::vector<boost::geometry::model::ring<point, false, false>> result;  
  
    // Declare/fill a linestring  
    boost::geometry::model::linestring<point> ls;  
    boost::geometry::read_wkt("LINESTRING(0 0,4 5,7 4,10 6)", ls);  
    boost::geometry::buffer(ls, result,  
                distance_strategy, side_strategy,  
                join_strategy, end_strategy, circle_strategy);  
  
        // Declare/fill a multi point  
    boost::geometry::model::multi_point<point> mp;  
    boost::geometry::read_wkt("MULTIPOINT((3 3),(4 4),(6 2))", mp);  
  
    // Create the buffer of a multi point  
    boost::geometry::buffer(mp, result,  
                distance_strategy, side_strategy,  
                join_strategy, end_strategy, circle_strategy);  
    return 0;  
}  
```  
This code works fine in boost 1.75. But it fails to compile with 1.80 raising the not implemented error.  
  
However, if the output type is changed from   
`std::vector<boost::geometry::model::ring<point, false, false>> result;`  
to  
`boost::geometry::model::multi_polygon<polygon> result;`  
it compiles fine with 1.8

---

## Comment 3

> Username: barendgehrels  
> Created at: 2022-11-16 12:47:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085#issuecomment-1316969281  

Thanks! I'll look soon.  
  
However, in hindsight, it might be intentional. A buffer of rings (or any other geometry type apart from one point) might cause holes. And they are not representable in a vector of rings.  
  
Isn't it better to change your code?

---

## Comment 4

> Username: ntalele  
> Created at: 2022-11-17 18:49:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085#issuecomment-1319061226  

@barendgehrels We use this call for very simple geometries and have specialization overload for other geometries. The reason we do this is because we have a lot of optimizations in our code for the ring_tag. Switching to multi polygon is definitely possible, but it might cause us some performance overhead. Hence, I was wondering if it would be possible to continue the support for a vector of rings as an output type for buffer.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2022-11-30 11:03:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085#issuecomment-1331978135  

Hi @ntalele , I looked in more detail.  
  
There were more changes to support Geometry Collections. Therefore I cannot change it exactly back as it was before.  
  
For example, in your code, if you change ring to polygon  
`std::vector<polygon> result;`  
it also worked fine in 1.75, but the whole vector-output support has disappeared.  
  
However, I created a workaround which works for both polygon and ring.  
```  
namespace boost { namespace geometry {  
  
template  
<  
    typename Input,  
    typename Output,  
    typename DistanceStrategy,  
    typename SideStrategy,  
    typename JoinStrategy,  
    typename EndStrategy,  
    typename PointStrategy  
>  
void buffer_work_around(Input const& geometry_in,  
                        Output& geometry_out,  
                        DistanceStrategy const& distance_strategy,  
                        SideStrategy const& side_strategy,  
                        JoinStrategy const& join_strategy,  
                        EndStrategy const& end_strategy,  
                        PointStrategy const& point_strategy)  
{  
    using geometry_type = typename boost::range_value<Output>::type;  
    typename strategies::buffer::services::default_strategy<geometry_type>::type strategies;  
    detail::no_rescale_policy rescale_policy;  
  
    detail::buffer::buffer_inserter<geometry_type>(geometry_in,  
                range::back_inserter(geometry_out),  
                distance_strategy,  
                side_strategy,  
                join_strategy,  
                end_strategy,  
                point_strategy,  
                strategies,  
                rescale_policy);  
}  
}}  
```  
  
I'm not sure if we can apply it to the code base, but for you this might be quite convenient.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2022-11-30 11:05:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085#issuecomment-1331980637  

It works also for that multipoint, so  
```  
    // Create the buffer of a multi point  
    result.clear();  
    boost::geometry::buffer_work_around(mp, result,  
                distance_strategy, side_strategy,  
                join_strategy, end_strategy, circle_strategy);      
```  
(you can put the clear also inside the workaround)

---

## Comment 7

> Username: barendgehrels  
> Created at: 2022-12-07 08:30:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085#issuecomment-1340580671  

Closing as possible to work around it.

---

## Comment 8

> Username: ntalele  
> Created at: 2022-12-07 17:50:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1085#issuecomment-1341345815  

Thanks! Will give this a try.
