# #781 - How to perform rtree query with not supported shape? [Closed]

> Username: cyang-kth  
> Created at: 2020-12-07 06:40:49 UTC  
> Updated at: 2020-12-08 17:51:27 UTC  
> Closed at: 2020-12-07 13:11:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/781  

I have a large set of points and I want to query a point _A_ with a distance range (min, max) to find all the points whose distance to _A_ lies in that range.  
  
How to implement that query using the rtree index?

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-12-07 13:11:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/781#issuecomment-739909275  

If you need all of the points then this would be a spatial query, not knn query. So you can pass a bounding box created for max distance and then filter the points that are closer to min or further to max:  
```  
rt.query(bgi::intersects(box{{a.x() - max, a.y() - max}, {a.x() + max, a.y() + max}})  
      && bgi::satisfies([&](auto const& v) {  
             auto const d = bg::distance(a, v);  
             return d >= min && d <= max;  
         }),  
         std::back_inserter(result));  
```

---

## Comment 2

> Username: cyang-kth  
> Created at: 2020-12-07 16:06:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/781#issuecomment-740013560  

Thanks for your reply. When both the min and max are quite big, the method based on intersection may need to examine a lot of points. I think it would be more efficient to customize the process of rtree iteration by comparing the min and max distance to a bounding box with this distance range. It seems to be define a new type of spatial predicate but I could not find any resources or example on that.   
  
https://www.boost.org/doc/libs/1_74_0/libs/geometry/doc/html/geometry/reference/spatial_indexes/group__predicates.html  
  
It seems that all the current predicates takes geometry as input or compare on value type, is there a way to customize the iteration process on rtree?

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-12-08 00:57:42 UTC  
> Updated at: 2020-12-08 01:04:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/781#issuecomment-740276395  

Currently there is no official way of defining a custom query.  
  
If you think that min can be big and taking it into account would result in pruning large parts of the tree, there are ways to achieve that.  
  
**Solution 1.**  
  
Pass negated predicate defining what should be rejected. Unfortunately for now it is not possible to pass circles (nspheres are not released yet, see Solution 2). So in addition to this:  
```  
   bgi::intersects(box{{a.x() - max, a.y() - max}, {a.x() + max, a.y() + max}})  
&& bgi::satisfies([&](auto const& v) {  
       auto const d = bg::distance(a, v);  
           return d >= min && d <= max;  
   }  
```  
you can pass a square inscribed in a circle with `min` radius:  
```  
double m = min / sqrt(2.0);  
   bgi::intersects(box{{a.x() - max, a.y() - max}, {a.x() + max, a.y() + max}})  
&& ! bgi::within(box{{a.x() - m, a.y() - m}, {a.x() + m, a.y() + m}})  
&& bgi::satisfies([&](auto const& v) {  
       auto const d = bg::distance(a, v);  
           return d >= min && d <= max;  
   }  
```  
  
**Solution 2**  
  
Some basic nsphere support is implemented in the extensions (https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions). You need develop branch for that. If you want to play with it you have to include https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/nsphere/nsphere.hpp  
  
**Solution 3**  
  
You could have your representation of a circle or even a ring and implement the `bg::intersection()` for boxes and points by yourself. See: https://stackoverflow.com/questions/19490435/boost-geometry-spatial-query-shapes

---

## Comment 4

> Username: cyang-kth  
> Created at: 2020-12-08 09:53:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/781#issuecomment-740513772  

Thanks a lot for your reply! I will have a look at those solutions.

---

## Comment 5

> Username: cyang-kth  
> Created at: 2020-12-08 14:25:53 UTC  
> Updated at: 2020-12-08 14:59:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/781#issuecomment-740650839  

Sorry to both you again.   
  
When I try solution 3 to implement the intersection on a customized `Ring` defined by a point and two radius values like following:  
  
```cpp  
typedef boost::geometry::model::point<double, 2,  
    boost::geometry::cs::cartesian> Point; // Point for rtree box  
typedef boost::geometry::model::box<Point> boost_box;  
struct Ring {  
  Point p;  
  double r1; // inner radius  
  double r2; // external radius  
};  
namespace boost { namespace geometry {  
namespace bg = boost::geometry;  
  template <> inline  
  bool intersects(boost_box const &b, Ring const& ring)  
  {...};  
}};  
```  
  
It cannot build with the error saying   
  
```  
/Users/canyang/Workspace/fmm/src/network/rtree.cpp:44:9: note: in instantiation of function template specialization 'boost::geometry::index::rtree<std::__1::pair<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>, unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::index::indexable<std::__1::pair<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>, unsigned int> >, boost::geometry::index::equal_to<std::__1::pair<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>, unsigned int> >, boost::container::new_allocator<std::__1::pair<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>, unsigned int> > >::query<boost::geometry::index::detail::predicates::spatial_predicate<FMM::NETWORK::Ring, boost::geometry::index::detail::predicates::intersects_tag, false>, std::__1::back_insert_iterator<std::__1::vector<unsigned int, std::__1::allocator<unsigned int> > > >' requested here  
  rtree.query(boost::geometry::index::intersects(ring),  
        ^  
/usr/local/include/boost/mpl/assert.hpp:83:5: note: candidate function not viable: no known conversion from 'boost::mpl::failed ************(boost::geometry::core_dispatch::geometry_id<void>::NOT_IMPLEMENTED_FOR_THIS_GEOMETRY_TYPE::************)(types<void>)' to 'typename assert<false>::type' (aka 'mpl_::assert<false>') for 1st argument  
int assertion_failed( typename assert<C>::type );  
```  
  
It seems that I have to register my own geometry first.   
https://www.boost.org/doc/libs/1_74_0/libs/geometry/doc/html/geometry/reference/adapted.html  
  
However, from the list above I cannot find a suitable concept for my Ring class. Is this way of defining geometry not compatible with Boost Geometry Concept. The reason that I did not use polygon is that the ring here can be defined by  
three configurations and there is no need to store a detailed externior coordinates.    
  
Thanks for your help.

---

## Comment 6

> Username: awulkiew  
> Created at: 2020-12-08 16:55:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/781#issuecomment-740759231  

You need 2 specializations of `bg::intersects()` one for the box type used by the R-tree (`rtree<...>::bounds_type`), and the other one for the point type you store. So you also have to add:  
```  
template <>  
inline bool intersects(Point const &p, Ring const& ring)  
{...};  
```  
Basically look at the compiler output, see what types are passed into `bg::intersects()` and implement specializations for these types. If these function templates are instantiated then the compiler will not instantiate the default one and will not check the concepts at all.  
  
This works for me with msvs-2017 and gcc-8.1:  
```  
#include <boost/geometry.hpp>  
#include <vector>  
  
namespace bg = boost::geometry;  
namespace bgi = boost::geometry::index;  
  
typedef bg::model::point<double, 2, bg::cs::cartesian> Point;  
typedef bg::model::box<Point> Box;  
  
namespace Abc {  
  
struct Ring {  
    Point p;  
    double r1;  
    double r2;  
};  
  
}  
  
namespace boost { namespace geometry {  
          
template <>  
inline bool intersects(Box const &b, Abc::Ring const& ring)  
{  
    return false;  
}  
  
template <>  
inline bool intersects(Point const &b, Abc::Ring const& ring)  
{  
    return false;  
}  
  
}}  
  
int main()  
{  
    bgi::rtree<std::pair<Point, size_t>, bgi::quadratic<16>> rt;  
  
    Abc::Ring ring;  
    std::vector<std::pair<Point, size_t>> vec;  
    rt.query(bgi::intersects(ring), std::back_inserter(vec));  
}  
```  
  
Note that there are problems with overloads (without `template<>`) with non-msvs compilers. In this case the order of includes and definitions is important. But it seems that explicit template specializations works fine.

---

## Comment 7

> Username: cyang-kth  
> Created at: 2020-12-08 17:25:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/781#issuecomment-740780426  

@awulkiew   
Thanks for your help. I also discovered that I need to implement the intersects for both Box type and Point type against the Ring. I test on Mac and Ubuntu, which compile successfully.   
  
![下载](https://user-images.githubusercontent.com/6810084/101518744-9f88b380-3982-11eb-896e-85f155261cf2.png)

---

## Comment 8

> Username: awulkiew  
> Created at: 2020-12-08 17:51:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/781#issuecomment-740797529  

That's great. The result looks nice too. Thanks for the feedback.
