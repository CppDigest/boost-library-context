# #254 Fix return_type of sub_range [Closed]

> Username: sdebionne  
> Created at: 2015-03-09 11:55:02 UTC  
> Updated at: 2015-03-09 15:33:17 UTC  
> Closed at: 2015-03-09 15:33:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/254  

IMHO, `return_type` should not be defined by reference to `geometry::ring_type<Geometry>::type` but by value.  
  
Here is a model of Polygon that demonstrates the problem, the key point being that the return type of `inners()` is a Range:  
  
```  
template <typename Point>  
struct polygon  
{  
  typedef boost::iterator_range<const Point *> ring_t;  
  typedef boost::iterator_range  
    <  
      typename std::vector<ring_t>::const_iterator  
    > rings_t;  
  
  ring_t outer() const { return rings.front(); }  
  rings_t inners() const { return rings_t(rings.begin() + 1, rings.end()); }  
  
  std::vector<ring_t> rings;  
};  
  
namespace boost { namespace geometry { namespace traits  
{  
  
template <typename Point> struct tag< polygon<const Point> > { typedef polygon_tag type; };  
  
template <typename Point> struct ring_mutable_type< polygon<Point> > { typedef typename polygon<Point>::ring_t type; };  
template <typename Point> struct ring_const_type< polygon<Point> > { typedef typename polygon<Point>::ring_t type; };  
  
template <typename Point> struct interior_mutable_type< polygon<Point> > { typedef typename polygon<Point>::rings_t type; };  
template <typename Point> struct interior_const_type< polygon<Point> > { typedef typename polygon<Point>::rings_t type; };  
  
}}} // namespace boost::geometry::traits  
```  
  
With such a model, that hopefully satisfy the Polygon concepts, the collection of ring returned by `inners()` is an rvalue. That make the expression `return range::at(geometry::interior_rings(geometry), ri);` returns a dangling reference (actually it does not compile).  
  
**IMPORTANT:** This PR is a solution to this specific problem, but requires more thinking since it's probably a breaking change for models whose interior ring type is a Container...

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-03-09 15:07:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/254#issuecomment-77870887  

A general remark. `ring_t` should be adapted to the Ring concept in order to work with Boost.Geometry algorithms. Furthermore the above concept adaptation lacks `exterior_ring` and `interior_rings` specializations. I'd also change `const Point` to `Point` in `tag` specialization. However this shouldn't be a problem for sub_range().  
  
Indeed, the problem is related to the return_type for Polygon. It should be of type `geometry::ring_return_type<Geometry>::type`. This is the reason why there are 2 metafunctions `ring_type` and `ring_return_type`. Your solution would also work but for containers a copy of a Ring would be returned which should of course be avoided. I fixed it in the develop branch. Thanks for testing the library for Ranges and catching this!

---

## Comment 2

> Username: sdebionne  
> Created_at: 2015-03-09 15:33:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/254#issuecomment-77876383  

Thank you for the fix (I'm glad that it was simpler after all) and I can confirm that it works. Thank you also for the advices.

---
