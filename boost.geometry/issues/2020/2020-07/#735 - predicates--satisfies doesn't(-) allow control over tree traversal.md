# #735 - predicates::satisfies doesn't(?) allow control over tree traversal [Open]

> Username: BenFrantzDale  
> Created at: 2020-07-14 21:02:58 UTC  
> Updated at: 2020-07-15 16:50:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/735  

AFAICT, `predicates::satisfies<Fun, Negated>` lets you give an arbitrary function object and then use it to, say, visit every `rtree::value_type` in an `rtree`. It seems like it just needs a second function, which could default to `[](auto&&...) { return true; }`, to let you control the traversal of the tree. For example, suppose I want to visit an `rtree<Point2D>` and visit all points in the (-,+) direction of the line y=x. I think(?) there's no way to do that right now. If I have `struct IsAboveLeft { bool operator()(const Point2D& pt) const { return pt[0] - pt[1] > 0; } };` then I could use `predicates::satisfies<IsAboveLeft>`, but it would be an O(n) query of my `rtree`. But if I could do something like  
```  
struct FindAboveLeft {  
    bool partially_contains(const Box&) const; // check if ay part of AABB is above-left of the line y=x  
    bool contains(const Value&) const; // check if the point is above-left of the line y=x  
};  
// ...  
rtree.query(satisfies<FindAboveLeft>(), std::back_inserter(results));  
```  
then the query could be O(log n) per result returned. As it is, I can't see how to get this behavior without injecting things into the `boost::geometry::index::predicates::detail` namespace. I think a predicate like this could be very expressive and allow for all sorts of interesting queries.

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-07-14 22:41:24 UTC  
> Updated at: 2020-07-14 22:42:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/735#issuecomment-658448483  

Yes, `satisfies` predicate is only applied to Values. It is not a spatial predicate.  
  
It is possible to perform such query by implementing a new class like `LineBoundedSpace` and then passing and object of this class in `bgi::intersects` into `rtree::query` after overloading `bg::intersects` for:  
- `LineBoundedSpace` and `Box`  
- `LineBoundedSpace` and `Value`/`Point2D`  
  
But I agree that an explicit user-defined spatial predicate allowing the users to define tree traversal and filtering of values could be useful. So I'll treat this issue as a feature request.

---

## Comment 2

> Username: BenFrantzDale  
> Created at: 2020-07-14 23:18:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/735#issuecomment-658458975  

I’m not sure I follow. Are you saying I would specialize `detail::spatial_predicate< LineBoundedSpace, ...>`? That feels wrong since I’d assume anything in `detail` is an implementation detail not a customization point...

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-07-15 00:59:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/735#issuecomment-658485427  

No, it is not required to touch anything in detail. You only has to overload:  
```  
namespace boost { namespace geometry {  
inline bool intersects(rtree_t::bounds_type const& box, MyLine const& l) { return true; }  
inline bool intersects(Point2D const& pt, MyLine const& l) { return true; }  
}}  
```  
and then call:  
```  
MyLine my_line;  
rtree.query(bgi::intersects(my_line), std::back_inserter(result));  
```  
  
See also: https://stackoverflow.com/questions/19490435/boost-geometry-spatial-query-shapes

---

## Comment 4

> Username: BenFrantzDale  
> Created at: 2020-07-15 11:49:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/735#issuecomment-658722162  

OK, with that I get a compile error: https://godbolt.org/z/3qxdPM  
  
I'll take a stab at implementing the multi-level `satisfies` equivalent.

---

## Comment 5

> Username: awulkiew  
> Created at: 2020-07-15 16:50:31 UTC  
> Updated at: 2020-07-15 16:50:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/735#issuecomment-658877780  

Yes, the compiler has problems with picking the overloads. It chooses to instantiate the original `bg::intersects` template, as mentioned here: https://stackoverflow.com/questions/19490435/boost-geometry-spatial-query-shapes and fails to do it.  
  
To force it to do it you can e.g. add some forward declarations before including the library:  
```  
#include <boost/geometry/geometries/geometries.hpp>  
  
namespace bg = boost::geometry;  
using Pt = bg::model::point<double, 2, bg::cs::cartesian>;  
  
struct RightHalfPlane;  
  
namespace boost::geometry {  
  
bool intersects(const bg::model::box<Pt>& box, const RightHalfPlane& x);  
bool intersects(const Pt& box, const RightHalfPlane& x);  
  
} // boost::geometry  
  
#include <boost/geometry/geometry.hpp>  
  
namespace bgi = boost::geometry::index;  
```  
  
then the rest of your code.
