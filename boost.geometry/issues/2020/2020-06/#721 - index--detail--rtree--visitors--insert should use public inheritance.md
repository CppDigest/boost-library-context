# #721 - index::detail::rtree::visitors::insert should use public inheritance [Closed]

> Username: jwakely  
> Created at: 2020-06-05 16:59:19 UTC  
> Updated at: 2020-06-06 01:21:49 UTC  
> Closed at: 2020-06-06 01:19:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/721  

[This](https://github.com/boostorg/geometry/blob/001274d72c528dfc97ff9db44efd600569f1e1a4/include/boost/geometry/index/detail/rtree/visitors/insert.hpp#L268) looks wrong and seems to be causing a build failure for FreeCAD:  
  
```  
// Default insert visitor  
template <typename Element, typename MembersHolder>  
class insert  
    : MembersHolder::visitor  
```  
  
This was changed with https://github.com/boostorg/geometry/commit/477627d16a3ea8907ee8ba3ee781a8924248efdc  
  
The resulting build failure is:  
  
```  
In file included from /usr/include/boost/geometry/index/detail/rtree/node/node.hpp:30,  
                 from /usr/include/boost/geometry/index/rtree.hpp:60,  
                 from /usr/include/boost/geometry/algorithms/detail/within/multi_point.hpp:33,  
                 from /usr/include/boost/geometry/algorithms/detail/within/implementation.hpp:42,  
                 from /usr/include/boost/geometry/algorithms/detail/covered_by/implementation.hpp:25,  
                 from /usr/include/boost/geometry/algorithms/covered_by.hpp:24,  
                 from /usr/include/boost/geometry/algorithms/detail/distance/multipoint_to_geometry.hpp:22,  
                 from /usr/include/boost/geometry/algorithms/detail/distance/implementation.hpp:26,  
                 from /usr/include/boost/geometry/algorithms/detail/comparable_distance/implementation.hpp:22,  
                 from /usr/include/boost/geometry/algorithms/comparable_distance.hpp:23,  
                 from /usr/include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp:28,  
                 from /usr/include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp:35,  
                 from /usr/include/boost/geometry/algorithms/buffer.hpp:41,  
                 from /usr/include/boost/geometry/geometry.hpp:63,  
                 from /usr/include/boost/geometry.hpp:17,  
                 from /builddir/build/BUILD/FreeCAD-0.18.4/src/Mod/Path/App/Area.cpp:35:  
/usr/include/boost/geometry/index/detail/rtree/node/variant_visitor.hpp: In instantiation of 'void boost::geometry::index::detail::rtree::apply_visitor(Visitor&, boost::variant<boost::geometry::index::detail::rtree::variant_leaf<Value, Parameters, Box, Allocators, Tag>, boost::geometry::index::detail::rtree::variant_internal_node<Value, Parameters, Box, Allocators, Tag> >&) [with Visitor = boost::geometry::index::detail::rtree::visitors::insert<WireJoiner::VertexInfo, boost::geometry::index::rtree<WireJoiner::VertexInfo, boost::geometry::index::linear<16>, WireJoiner::PntGetter>::members_holder, boost::geometry::index::detail::rtree::insert_default_tag>; Value = WireJoiner::VertexInfo; Parameters = boost::geometry::index::linear<16>; Box = boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >; Allocators = boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<WireJoiner::VertexInfo>, WireJoiner::VertexInfo, boost::geometry::index::linear<16>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>; Tag = boost::geometry::index::detail::rtree::node_variant_static_tag]':  
/usr/include/boost/geometry/index/rtree.hpp:1545:37:   required from 'void boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::raw_insert(const value_type&) [with Value = WireJoiner::VertexInfo; Parameters = boost::geometry::index::linear<16>; IndexableGetter = WireJoiner::PntGetter; EqualTo = boost::geometry::index::equal_to<WireJoiner::VertexInfo>; Allocator = boost::container::new_allocator<WireJoiner::VertexInfo>; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::value_type = WireJoiner::VertexInfo]'  
/usr/include/boost/geometry/index/rtree.hpp:688:15:   required from 'void boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::insert(const value_type&) [with Value = WireJoiner::VertexInfo; Parameters = boost::geometry::index::linear<16>; IndexableGetter = WireJoiner::PntGetter; EqualTo = boost::geometry::index::equal_to<WireJoiner::VertexInfo>; Allocator = boost::container::new_allocator<WireJoiner::VertexInfo>; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::value_type = WireJoiner::VertexInfo]'  
/builddir/build/BUILD/FreeCAD-0.18.4/src/Mod/Path/App/Area.cpp:670:40:   required from here  
/usr/include/boost/geometry/index/detail/rtree/node/variant_visitor.hpp:51:25: error: no matching function for call to 'apply_visitor(boost::geometry::index::detail::rtree::visitors::insert<WireJoiner::VertexInfo, boost::geometry::index::rtree<WireJoiner::VertexInfo, boost::geometry::index::linear<16>, WireJoiner::PntGetter>::members_holder, boost::geometry::index::detail::rtree::insert_default_tag>&, boost::variant<boost::geometry::index::detail::rtree::variant_leaf<WireJoiner::VertexInfo, boost::geometry::index::linear<16>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<WireJoiner::VertexInfo>, WireJoiner::VertexInfo, boost::geometry::index::linear<16>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<WireJoiner::VertexInfo, boost::geometry::index::linear<16, 4>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<WireJoiner::VertexInfo>, WireJoiner::VertexInfo, boost::geometry::index::linear<16, 4>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >&)'  
   51 |     boost::apply_visitor(v, n);  
      |     ~~~~~~~~~~~~~~~~~~~~^~~~~~  
In file included from /usr/include/boost/variant/apply_visitor.hpp:16,  
                 from /usr/include/boost/geometry/algorithms/append.hpp:26,  
                 from /usr/include/boost/geometry/algorithms/detail/assign_values.hpp:33,  
                 from /usr/include/boost/geometry/algorithms/detail/assign_indexed_point.hpp:21,  
                 from /usr/include/boost/geometry/policies/relate/intersection_points.hpp:20,  
                 from /usr/include/boost/geometry/strategies/intersection_strategies.hpp:20,  
                 from /usr/include/boost/geometry/strategies/strategies.hpp:39,  
                 from /usr/include/boost/geometry/geometry.hpp:58,  
                 from /usr/include/boost/geometry.hpp:17,  
                 from /builddir/build/BUILD/FreeCAD-0.18.4/src/Mod/Path/App/Area.cpp:35:  
/usr/include/boost/variant/detail/apply_visitor_unary.hpp:46:1: note: candidate: 'template<class Visitor, class Visitable> typename Visitor::result_type boost::apply_visitor(Visitor&, Visitable&&)'  
   46 | apply_visitor(Visitor& visitor, Visitable&& visitable)  
      | ^~~~~~~~~~~~~  
/usr/include/boost/variant/detail/apply_visitor_unary.hpp:46:1: note:   template argument deduction/substitution failed:  
/usr/include/boost/variant/detail/apply_visitor_unary.hpp: In substitution of 'template<class Visitor, class Visitable> typename Visitor::result_type boost::apply_visitor(Visitor&, Visitable&&) [with Visitor = boost::geometry::index::detail::rtree::visitors::insert<WireJoiner::VertexInfo, boost::geometry::index::rtree<WireJoiner::VertexInfo, boost::geometry::index::linear<16>, WireJoiner::PntGetter>::members_holder, boost::geometry::index::detail::rtree::insert_default_tag>; Visitable = boost::variant<boost::geometry::index::detail::rtree::variant_leaf<WireJoiner::VertexInfo, boost::geometry::index::linear<16>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<WireJoiner::VertexInfo>, WireJoiner::VertexInfo, boost::geometry::index::linear<16>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<WireJoiner::VertexInfo, boost::geometry::index::linear<16, 4>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<WireJoiner::VertexInfo>, WireJoiner::VertexInfo, boost::geometry::index::linear<16, 4>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >&]':  
/usr/include/boost/geometry/index/detail/rtree/node/variant_visitor.hpp:51:25:   required from 'void boost::geometry::index::detail::rtree::apply_visitor(Visitor&, boost::variant<boost::geometry::index::detail::rtree::variant_leaf<Value, Parameters, Box, Allocators, Tag>, boost::geometry::index::detail::rtree::variant_internal_node<Value, Parameters, Box, Allocators, Tag> >&) [with Visitor = boost::geometry::index::detail::rtree::visitors::insert<WireJoiner::VertexInfo, boost::geometry::index::rtree<WireJoiner::VertexInfo, boost::geometry::index::linear<16>, WireJoiner::PntGetter>::members_holder, boost::geometry::index::detail::rtree::insert_default_tag>; Value = WireJoiner::VertexInfo; Parameters = boost::geometry::index::linear<16>; Box = boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >; Allocators = boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<WireJoiner::VertexInfo>, WireJoiner::VertexInfo, boost::geometry::index::linear<16>, boost::geometry::model::box<boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>; Tag = boost::geometry::index::detail::rtree::node_variant_static_tag]'  
/usr/include/boost/geometry/index/rtree.hpp:1545:37:   required from 'void boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::raw_insert(const value_type&) [with Value = WireJoiner::VertexInfo; Parameters = boost::geometry::index::linear<16>; IndexableGetter = WireJoiner::PntGetter; EqualTo = boost::geometry::index::equal_to<WireJoiner::VertexInfo>; Allocator = boost::container::new_allocator<WireJoiner::VertexInfo>; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::value_type = WireJoiner::VertexInfo]'  
/usr/include/boost/geometry/index/rtree.hpp:688:15:   required from 'void boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::insert(const value_type&) [with Value = WireJoiner::VertexInfo; Parameters = boost::geometry::index::linear<16>; IndexableGetter = WireJoiner::PntGetter; EqualTo = boost::geometry::index::equal_to<WireJoiner::VertexInfo>; Allocator = boost::container::new_allocator<WireJoiner::VertexInfo>; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::value_type = WireJoiner::VertexInfo]'  
/builddir/build/BUILD/FreeCAD-0.18.4/src/Mod/Path/App/Area.cpp:670:40:   required from here  
/usr/include/boost/variant/detail/apply_visitor_unary.hpp:46:1: error: 'typedef void boost::static_visitor<void>::result_type' is inaccessible within this context  
```  
  
Note the final line, which complains that the visitor's result type is inaccessible, which matches my observation that the base class should be public.

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-06-06 01:21:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/721#issuecomment-639939638  

Thanks!
