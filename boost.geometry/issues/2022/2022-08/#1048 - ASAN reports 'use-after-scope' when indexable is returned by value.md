# #1048 - ASAN reports 'use-after-scope' when indexable is returned by value. [Closed]

> Username: 1uc  
> Created at: 2022-08-02 18:07:47 UTC  
> Updated at: 2022-12-01 09:38:40 UTC  
> Closed at: 2022-12-01 09:38:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1048  

The CLang AddressSanitizer will report a `stack-use-after-scope` when inserting elements into an `bgi::rtree` when computing the bounding box on the fly, e.g.,  
```  
namespace demo {  
using Point3D = bg::model::point<double, 3, bg::cs::cartesian>;  
using Box3D = bg::model::box<Point3D>;  
  
struct Sphere {  
  std::array<double, 3> center;  
  double radius;  
};  
}  
  
namespace boost { namespace geometry { namespace index {  
  
template <>  
struct indexable<demo::Sphere> {  
  typedef demo::Sphere V;  
  typedef demo::Box3D const result_type;  
  
  result_type operator()(const demo::Sphere& sphere) const {  
    auto [x, y, z] = sphere.center;  
    auto r = sphere.radius;  
    return {{x - r, y - r, z - r}, {x + r, y + r, z + r}};  
  }  
};  
  
}}}  
```  
  
The issue happens when inserting, e.g.,  
```  
  auto spheres = demo::make_spheres();  
  auto index = bgi::rtree<demo::Sphere, bgi::linear<16, 4>>();  
  index.insert(spheres.begin(), spheres.end());  # use-after-scope  
```  
  
A complete reproducer, including (annotated) ASAN output, for Boost 1.79.0 can be found here:  
https://github.com/1uc/rtree-bbox-by-value  
  
The first part of the stack trace is:  
```  
READ of size 8 at 0x7ffc7a68e7d0 thread T0  
    #0 0x5633e41c06d2 in [...] /usr/include/boost/geometry/geometries/point.hpp:290:27  
    #1 0x5633e41c0694 in [...] /usr/include/boost/geometry/core/access.hpp:169:16  
    #2 0x5633e41c0668 in [...] /usr/include/boost/geometry/core/access.hpp:283:12  
    #3 0x5633e41c0640 in [...] /usr/include/boost/geometry/geometries/box.hpp:211:16  
    #4 0x5633e41c0614 in [...] /usr/include/boost/geometry/core/access.hpp:98:16  
    #5 0x5633e41c05d8 in [...] /usr/include/boost/geometry/core/access.hpp:350:12  
    #6 0x5633e41e430d in [...] /usr/include/boost/geometry/index/detail/bounded_view.hpp:137:17  
    #7 0x5633e41e42c4 in [...] /usr/include/boost/geometry/index/detail/bounded_view.hpp:256:27  
    #8 0x5633e41e42a4 in [...] /usr/include/boost/geometry/core/access.hpp:98:16  
    #9 0x5633e41e3f78 in [...] /usr/include/boost/geometry/core/access.hpp:350:12  
    #10 0x5633e41e3736 in [...] /usr/include/boost/geometry/index/detail/rtree/linear/redistribute_elements.hpp:123:38  
    #11 0x5633e41e290c in [...] /usr/include/boost/geometry/index/detail/rtree/linear/redistribute_elements.hpp:296:9  
    ...  
```  
  
I think the interesting lines are (the code includes context, the actual line from the trace is marked by a trailing comment `// l*`):  
```  
    #6 0x5633e41e430d in [...] /usr/include/boost/geometry/index/detail/bounded_view.hpp:137:17  
  
      template <typename BoxIn, typename Box, typename Strategy, typename CSTag>  
      struct bounded_view_base<BoxIn, Box, Strategy, box_tag, box_tag, CSTag>  
      {  
      public:  
          typedef typename geometry::coordinate_type<Box>::type coordinate_type;  
  
          bounded_view_base(BoxIn const& box, Strategy const& )  
              : m_box(box)  
          {}  
  
          template <std::size_t Dimension>  
          inline coordinate_type get_min() const  
          {  
              return boost::numeric_cast<coordinate_type>(  
                      geometry::get<min_corner, Dimension>(m_box) ); // l137  
          }  
  
          /* ... */  
  
      private:  
          BoxIn const& m_box;  
      };  
  
    #10 0x5633e41e3736 in [...] /usr/include/boost/geometry/index/detail/rtree/linear/redistribute_elements.hpp:123:38  
  
      template <typename Elements, typename Parameters, typename Translator, typename Tag, size_t DimensionIndex>  
      struct find_greatest_normalized_separation  
      {  
          typedef typename Elements::value_type element_type;  
          typedef typename rtree::element_indexable_type<element_type, Translator>::type indexable_type;  
          typedef typename coordinate_type<indexable_type>::type coordinate_type;  
  
          typedef std::conditional_t</* ... */> separation_type;  
  
          typedef typename geometry::point_type<indexable_type>::type point_type;  
          typedef geometry::model::box<point_type> bounds_type;  
          typedef index::detail::bounded_view  
              <  
                  indexable_type, bounds_type,  
                  typename index::detail::strategy_type<Parameters>::type  
              > bounded_view_type;  
  
          static inline void apply(Elements const& elements,  
                                   Parameters const& parameters,  
                                   Translator const& translator,  
                                   separation_type & separation,  
                                   size_t & seed1,  
                                   size_t & seed2)  
          {  
              const size_t elements_count = parameters.get_max_elements() + 1;  
              BOOST_GEOMETRY_INDEX_ASSERT(elements.size() == elements_count, "unexpected number of elements");  
              BOOST_GEOMETRY_INDEX_ASSERT(2 <= elements_count, "unexpected number of elements");  
  
              typename index::detail::strategy_type<Parameters>::type const&  
                  strategy = index::detail::get_strategy(parameters);  
  
              // find the lowest low, highest high  
              bounded_view_type bounded_indexable_0(rtree::element_indexable(elements[0], translator),  
                                                    strategy);  
  
              coordinate_type lowest_low = geometry::get<min_corner, DimensionIndex>(bounded_indexable_0); // l123  
```  
Interlude: to the best of my knowledge, a const reference to a temporary extends the lifetime of the temporary. However, if the const reference is a member of a struct, the lifetime of the temporary is not extended [1][2].  
[1]: https://herbsutter.com/2008/01/01/gotw-88-a-candidate-for-the-most-important-const/  
[2]: https://stackoverflow.com/questions/15513734/const-reference-as-class-member  
  
Therefore, on the second to last line it seems like a const reference to the temporary, i.e. the bounding box which is computed on the fly, is stored in `bounded_view_type` in a manner that will not extend the lifetime of the temporary. Hence, on the last line a  `stack-use-after-scope` occurs.  
  
We hit this issue in production and are wondering if this is expected, for example because the library expects that the 'indexable' is returned as a const reference to an object with sufficiently long lifetime. We've looked through the documentation and the closest we found was, a suggestion that for performance reasons it might be better to not compute the 'indexable' on the fly. However, in our case we're rather reluctant to increase the memory requirements by a factor of two or three just to avoid a few flops.

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-08-03 12:01:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1048#issuecomment-1203857957  

Thanks for this report.  
  
AFAIR the assumption was that the indexable is stored inside an object of type `value_type` and returned by reference. It's because this may be done quite frequently so this operation has to be lightweight, etc.  
  
But I agree that returning non-references from indexable getter should be allowed to avoid issues such as this one. So I'll consider this to be a bug.

---

## Comment 2

> Username: 1uc  
> Created at: 2022-08-03 17:06:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1048#issuecomment-1204238886  

Thank you for your quick response. I'm not sure I'll be of much use improving the issue, but if there's some other way we can help please ask.

---

## Comment 3

> Username: vissarion  
> Created at: 2022-12-01 09:38:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1048#issuecomment-1333485645  

fixed by https://github.com/boostorg/geometry/pull/1080
