# #902 - Compilation error when adapting geometry model with point iterator returning by value [Open]

> Username: peter-popov  
> Created at: 2021-08-21 11:54:17 UTC  
> Updated at: 2021-08-26 22:16:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/902  

## Problem  
I am adapting my geometry model to work with boost::geometry(as described in the [tutorial](https://www.boost.org/doc/libs/1_76_0/libs/geometry/doc/html/geometry/examples/example__adapting_a_legacy_geometry_object_model.html)).   
  
However, my point iterator needs to return a value rather than a reference. In my case, there is no specific class to represent a point and I need to create points on the fly from the binary representation(see example below).  
  
When adapting the polygon concept I receive the following complication issue:  
```  
[build] /opt/homebrew/include/boost/geometry/iterators/concatenate_iterator.hpp:105:16: warning: returning reference to local temporary object [-Wreturn-stack-address]  
[build]         return *m_it1;  
[build]                ^~~~~~  
[build] /opt/homebrew/include/boost/iterator/iterator_facade.hpp:550:20: note: in instantiation of member function 'boost::geometry::concatenate_iterator<PointIterator<boost::geometry::model::d2::point_xy<double>, std::__1::__wrap_iter<const double *>>, boost::geometry::flatten_iterator<std::__1::__wrap_iter<const MyRing *>, PointIterator<boost::geometry::model::d2::point_xy<double>, std::__1::__wrap_iter<const double *>>, const boost::geometry::model::d2::point_xy<double>, boost::geometry::dispatch::points_begin<const MyRing, boost::geometry::ring_tag>, boost::geometry::dispatch::points_end<const MyRing, boost::geometry::ring_tag>>, const boost::geometry::model::d2::point_xy<double>, const boost::geometry::model::d2::point_xy<double> &>::dereference' requested here  
[build]           return f.dereference();  
[build]                    ^  
[build] /opt/homebrew/include/boost/iterator/iterator_facade.hpp:656:42: note: in instantiation of function template specialization 'boost::iterators::iterator_core_access::dereference<boost::geometry::concatenate_iterator<PointIterator<boost::geometry::model::d2::point_xy<double>, std::__1::__wrap_iter<const double *>>, boost::geometry::flatten_iterator<std::__1::__wrap_iter<const MyRing *>, PointIterator<boost::geometry::model::d2::point_xy<double>, std::__1::__wrap_iter<const double *>>, const boost::geometry::model::d2::point_xy<double>, boost::geometry::dispatch::points_begin<const MyRing, boost::geometry::ring_tag>, boost::geometry::dispatch::points_end<const MyRing, boost::geometry::ring_tag>>, const boost::geometry::model::d2::point_xy<double>>>' requested here  
[build]             return iterator_core_access::dereference(this->derived());  
[build]                                          ^  
[build] /opt/homebrew/include/boost/iterator/iterator_adaptor.hpp:294:18: note: in instantiation of member function 'boost::iterators::detail::iterator_facade_base<boost::geometry::concatenate_iterator<PointIterator<boost::geometry::model::d2::point_xy<double>, std::__1::__wrap_iter<const double *>>, boost::geometry::flatten_iterator<std::__1::__wrap_iter<const MyRing *>, PointIterator<boost::geometry::model::d2::point_xy<double>, std::__1::__wrap_iter<const double *>>, const boost::geometry::model::d2::point_xy<double>, boost::geometry::dispatch::points_begin<const MyRing, boost::geometry::ring_tag>, boost::geometry::dispatch::points_end<const MyRing, boost::geometry::ring_tag>>, const boost::geometry::model::d2::point_xy<double>>, const boost::geometry::model::d2::point_xy<double>, boost::iterators::bidirectional_traversal_tag, const boost::geometry::model::d2::point_xy<double> &, long, false, false>::operator*' requested here  
[build]         { return *m_iterator; }  
[build]                  ^  
[build] /opt/homebrew/include/boost/iterator/iterator_facade.hpp:550:20: note: in instantiation of member function 'boost::iterators::iterator_adaptor<boost::geometry::point_iterator<const MyPolygon>, boost::geometry::concatenate_iterator<PointIterator<boost::geometry::model::d2::point_xy<double>, std::__1::__wrap_iter<const double *>>, boost::geometry::flatten_iterator<std::__1::__wrap_iter<const MyRing *>, PointIterator<boost::geometry::model::d2::point_xy<double>, std::__1::__wrap_iter<const double *>>, const boost::geometry::model::d2::point_xy<double>, boost::geometry::dispatch::points_begin<const MyRing, boost::geometry::ring_tag>, boost::geometry::dispatch::points_end<const MyRing, boost::geometry::ring_tag>>, const boost::geometry::model::d2::point_xy<double>>, boost::use_default, boost::use_default, boost::use_default, boost::use_default>::dereference' requested here  
[build]           return f.dereference();  
[build]                    ^  
[build] /opt/homebrew/include/boost/iterator/iterator_facade.hpp:656:42: note: in instantiation of function template specialization 'boost::iterators::iterator_core_access::dereference<boost::geometry::point_iterator<const MyPolygon>>' requested here  
[build]             return iterator_core_access::dereference(this->derived());  
[build]                                          ^  
[build] /opt/homebrew/include/boost/geometry/algorithms/detail/distance/linear_to_linear.hpp:68:26: note: (skipping 2 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
[build]                 >::apply(*points_begin(linear2), linear1, strategy);  
[build]                          ^  
[build] /opt/homebrew/include/boost/geometry/algorithms/detail/distance/interface.hpp:128:16: note: in instantiation of member function 'boost::geometry::detail::distance::linear_to_areal<MyLineString, MyPolygon, boost::geometry::strategy::distance::projected_point<>>::apply' requested here  
[build]             >::apply(geometry1, geometry2, strategy_type());  
[build]                ^  
[build] /opt/homebrew/include/boost/geometry/algorithms/detail/distance/interface.hpp:151:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::default_strategy>::apply<MyLineString, MyPolygon>' requested here  
[build]             >::apply(geometry1, geometry2, strategy);  
[build]                ^  
[build] /opt/homebrew/include/boost/geometry/algorithms/detail/distance/interface.hpp:378:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<MyLineString, MyPolygon>::apply<boost::geometry::default_strategy>' requested here  
[build]                >::apply(geometry1, geometry2, strategy);  
[build]                   ^  
[build] /opt/homebrew/include/boost/geometry/algorithms/detail/distance/interface.hpp:403:22: note: in instantiation of function template specialization 'boost::geometry::distance<MyLineString, MyPolygon, boost::geometry::default_strategy>' requested here  
[build]     return geometry::distance(geometry1, geometry2, default_strategy());  
```  
  
## Reproducing  
To reproduce a problem I wrote a simple program that creates polygons and linestrings from separate vectors for X and Y:  
  
<details>  
<summary>Click here to see a simple program to reproduce the issue</summary>  
<br>  
      
```cpp  
#include <boost/geometry.hpp>  
#include <boost/range.hpp>  
#include <iostream>  
  
using namespace std;  
namespace bg = boost::geometry;  
  
// Iterator creating points from two separate iterators for Xs and Ys  
template <class T, class I>  
class PointIterator  
    : public boost::iterator_facade<PointIterator<T, I>, T,  
                                    boost::random_access_traversal_tag, T> {  
public:  
  friend class boost::iterator_core_access;  
  
  PointIterator() {}  
  PointIterator(I ix, I iy) : _ix(ix), _iy(iy) {}  
    
  // I WANT RETURN BY VALUE HERE!!!  
  T dereference() const { return T{*_ix, *_iy}; }  
    
  void increment() {  
    ++_ix;  
    ++_iy;  
  }  
  void decrement() {  
    --_ix;  
    --_iy;  
  }  
  void advance(size_t n) {  
    _ix += n;  
    _iy += n;  
  }  
  typename PointIterator<T, I>::difference_type  
  distance_to(const PointIterator<T, I>& other) const {  
    assert(distance(this->_ix, other._ix) == distance(this->_iy, other._iy));  
    return other._ix - this->_ix;  
  }  
  bool equal(const PointIterator<T, I>& other) const {  
    return this->_ix == other._ix && this->_iy == other._iy;  
  }  
private:  
  I _ix, _iy;  
};  
  
// Implementing my "geometry model" below  
using MyPoint = bg::model::d2::point_xy<double>;  
  
struct MyLineString {  
  using const_iterator = PointIterator<MyPoint, vector<double>::const_iterator>;  
  
  const_iterator begin() const {  
    return const_iterator(_x.cbegin(), _y.cbegin());  
  }  
  const_iterator end() const { return const_iterator(_x.cend(), _y.cend()); }  
  
  vector<double> _x, _y;  
};  
  
struct MyRing {  
  using const_iterator = PointIterator<MyPoint, vector<double>::const_iterator>;  
  
  const_iterator begin() const {  
    return const_iterator(_x.cbegin(), _y.cbegin());  
  }  
  const_iterator end() const { return const_iterator(_x.cend(), _y.cend()); }  
  
  vector<double> _x, _y;  
};  
  
struct MyPolygon {  
  using const_iterator = vector<MyRing>::const_iterator;  
  
  MyRing exterior;  
  vector<MyRing> interior; // We will not use it here  
};  
  
// Implementing boost::geometry traits for my types  
namespace boost::geometry::traits {  
  
template <> struct tag<MyLineString> { using type = linestring_tag; };  
template <> struct tag<MyRing> { using type = ring_tag; };  
template <> struct tag<MyPolygon> { using type = polygon_tag; };  
  
template <> struct ring_const_type<MyPolygon> { using type = const MyRing&; };  
template <> struct ring_mutable_type<MyPolygon> { using type = const MyRing&; };  
template <> struct interior_const_type<MyPolygon> {  
  using type = const boost::iterator_range<MyPolygon::const_iterator>;  
};  
template <> struct interior_mutable_type<MyPolygon> {  
  using type = boost::iterator_range<MyPolygon::const_iterator>;  
};  
  
template <> struct exterior_ring<MyPolygon> {  
  static MyRing const& get(MyPolygon const& p) { return p.exterior; }  
};  
  
template <> struct interior_rings<MyPolygon> {  
  static const auto get(MyPolygon const& p) {  
    return boost::iterator_range<MyPolygon::const_iterator>(p.interior.cbegin(),  
                                                            p.interior.cend());  
  }  
};  
  
} // namespace boost::geometry::traits  
  
// Implementing range traits  
namespace boost {  
template <> struct range_iterator<MyLineString> {  
  using type = MyLineString::const_iterator;  
};  
template <> struct range_iterator<MyRing> {  
  using type = MyRing::const_iterator;  
};  
} // namespace boost  
  
// Compute distance between linestring and polygon  
int main() {  
  MyPolygon polygon{MyRing{  
                        {1, 1, 2, 2, 1}, // x coordinates  
                        {1, 2, 2, 1, 1}  // y coordinates  
                    },  
                    {}};  
  MyLineString linestring{  
      {-1, 1}, // x coordinates  
      {1, -1}  // y coordinates  
  };  
  
  cout << "Distance = " << bg::distance(linestring, polygon) << endl;  
}  
```  
      
</details>  
  
## Possible cause  
  
I seems that issue can be in how the [geometry::detail::point_iterator<Polygon>](https://github.com/boostorg/geometry/blob/665305defa72f4de8777666c632bdd0436666343/include/boost/geometry/iterators/detail/point_iterator/iterator_type.hpp#L63) is defined. It is a combination of `concatenate_iterator` and `flatten_iterator` which both assume reference by default instead of deriving the reference type from the incoming point iterators:  
```cpp  
template <typename Polygon>  
class iterator_type<Polygon, polygon_tag>  
{  
private:  
    typedef typename inner_range_type<Polygon>::type inner_range;  
  
public:  
    typedef concatenate_iterator  
        <  
            typename boost::range_iterator<inner_range>::type,  
            flatten_iterator  
                <  
                    typename boost::range_iterator  
                        <  
                            typename geometry::interior_type<Polygon>::type  
                        >::type,  
                    typename iterator_type<inner_range>::type,  
                    typename value_type<Polygon>::type, // Value  
                    dispatch::points_begin<inner_range>,  
                    dispatch::points_end<inner_range>  
                    //, Reference = Value& !!!  
                >,  
            typename value_type<Polygon>::type // Value  
            //, Reference = Value& !!!  
        > type;  
};  
```  
      
Indeed, the test program above works when I add the following specialization:  
```cpp  
namespace boost::geometry::detail::point_iterator {  
  
template <> class iterator_type<const MyPolygon, polygon_tag> {  
private:  
  typedef typename inner_range_type<const MyPolygon>::type inner_range;  
  
public:  
  using type = concatenate_iterator<  
      typename boost::range_iterator<inner_range>::type,  
      flatten_iterator<  
          typename boost::range_iterator<  
              typename geometry::interior_type<const MyPolygon>::type>::type,  
          typename iterator_type<inner_range>::type,  
          typename value_type<const MyPolygon>::type,  
          dispatch::points_begin<inner_range>,  
          dispatch::points_end<inner_range>,  
          typename value_type<const MyPolygon>::type>, // Provide reference type  
      typename value_type<const MyPolygon>::type,  
      typename value_type<const MyPolygon>::type>; // Provide reference type  
};  
  
} // namespace boost::geometry::detail::point_iterator  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-08-21 20:03:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/902#issuecomment-903169962  

Right, in general we tend to support non-true references so it's possible that this should be `typename boost::range_reference<Polygon>::type`. I have to check it to be sure. It's possible that real reference is here for a reason. It's possible that one of these iterators or the code using them would be silently broken by this change, e.g. there could be dangling references somewhere.  
  
Another option would be to stop using these iterators internally in the library and instead use algorithm like `for_each_point()`.

---

## Comment 2

> Username: peter-popov  
> Created at: 2021-08-24 12:08:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/902#issuecomment-904580369  

Thanks for your answer. I cannot comment about using `for_each_point()`. It sounds like a better option for a library clients since it doesn't require a random access iterators. But I assume that requirement was added for a reason.   
  
In my experiment, I had to do a similar workaround for MultiLineString and MultiPolygon. After this, I've done tests trying some of the algorithms with different combinations of input geometries. It seems to work.  
  
I also tried to return a non-true reference from polygon interior and multi-geometry ranges. Unfotunatelly some of the algorithms(e.g. `covered_by` and `intersection`) didn't work at all. I got different combinations of compilation and runtime errors. Most likely the reason is dangling references in objects like `identity_view` or `boundary_view`.

---

## Comment 3

> Username: awulkiew  
> Created at: 2021-08-24 15:17:18 UTC  
> Updated at: 2021-08-24 15:18:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/902#issuecomment-904734559  

I skimmed through the code using these iterators and there is at least one place in the library that relies on the return of true reference. It's in the implementation of centroid of polygon. I stopped searching after finding one. So at least in this case (and I'd assume there may be more there will be a dangling reference so the correctness of the program will be more or less random and depend on wheter or not this memory is rewritten or not.  
  
I also looked into the possibility of getting rid of these iterators and unfortunately it'd require to rewrite some of the algorithms more than I hoped it would. So this wouldn't be a trivial fix.  
  
Anyway I think it's worth doing it and I'm going to treat this issue as a bug.

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-08-26 20:21:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/902#issuecomment-906716730  

@peter-popov Could you share your polygon class or at least something similar?

---

## Comment 5

> Username: peter-popov  
> Created at: 2021-08-26 20:27:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/902#issuecomment-906720035  

@awulkiew there is a simple `MyPolygon` class in the test program in the "Reproducing" section above.

---

## Comment 6

> Username: awulkiew  
> Created at: 2021-08-26 22:16:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/902#issuecomment-906780034  

@peter-popov Sorry I missed that, thanks. I was curious why doesn't it compile for you so I made this fix:  
https://github.com/boostorg/geometry/compare/develop...awulkiew:fix/remove_point_iterator  
It seems to work properly for `distance(ls, poly)`, `covered_by(ls, poly)`, `covered_by(poly, poly)` and `intersection(poly, poly, mpoly)` with msvc and clang. Of course multipolygon cannot be a type storing your polygons because your polygons cannot be modified. Could you check if this works for you?  
  
Anyway, this is only a quick test. I still have to go through all of the places where `point_iterator` is used and decide if changing it to `for_each_point` is really the best option.  
  
Out of curiosity, why do you store xs and ys separately?
