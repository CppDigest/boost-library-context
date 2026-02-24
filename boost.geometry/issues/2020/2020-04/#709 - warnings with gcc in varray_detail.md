# #709 - warnings with gcc in varray_detail [Closed]

> Username: tower120  
> Created at: 2020-04-30 16:52:02 UTC  
> Updated at: 2020-05-13 16:34:05 UTC  
> Closed at: 2020-05-13 14:55:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/709  

https://wandbox.org/permlink/rK0u8KeoiRdJRuc4  
  
```cpp  
#include <boost/geometry/index/rtree.hpp>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point.hpp>  
#include <boost/geometry/geometries/box.hpp>  
#include <vector>  
#include <iostream>  
  
namespace bg = boost::geometry;  
namespace bgi = bg::index;  
using  point  = bg::model::point <int, 2, bg::cs::cartesian>;  
using  pointI = std::pair<point, std::size_t>;  
  
int main()  
{  
    std::vector<pointI> cloud;  
    bgi::rtree<pointI, bgi::quadratic<16> > rtree(cloud);  
}  
```  
  
Output:  
```  
In file included from /opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray.hpp:41,  
                 from /opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/node/node.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/rtree.hpp:60,  
                 from prog.cc:1:  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray_detail.hpp: In instantiation of 'void boost::geometry::index::detail::varray_detail::construct_copy_dispatch(I, const V&, const mpl_::bool_<true>&) [with I = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*; V = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>]':  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray_detail.hpp:568:28:   required from 'void boost::geometry::index::detail::varray_detail::construct(const DisableTrivialInit&, I, const P&) [with DisableTrivialInit = boost::integral_constant<bool, false>; I = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*; P = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray.hpp:644:22:   required from 'void boost::geometry::index::detail::varray<Value, Capacity>::push_back(const value_type&) [with Value = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>; long unsigned int Capacity = 17; boost::geometry::index::detail::varray<Value, Capacity>::value_type = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/pack_create.hpp:298:13:   required from 'static boost::geometry::index::detail::rtree::pack<MembersHolder>::internal_element boost::geometry::index::detail::rtree::pack<MembersHolder>::per_level(EIt, EIt, const box_type&, boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type, const boost::geometry::index::detail::rtree::pack<MembersHolder>::subtree_elements_counts&, const parameters_type&, const translator_type&, boost::geometry::index::detail::rtree::pack<MembersHolder>::allocators_type&) [with EIt = __gnu_cxx::__normal_iterator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, __gnu_cxx::__normal_iterator<const std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > > >*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, __gnu_cxx::__normal_iterator<const std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > > >, std::allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, __gnu_cxx::__normal_iterator<const std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > > > > > >; MembersHolder = boost::geometry::index::rtree<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16> >::members_holder; boost::geometry::index::detail::rtree::pack<MembersHolder>::internal_element = boost::geometry::index::detail::rtree::ptr_pair<boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::variant<boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >*>; boost::geometry::index::detail::rtree::pack<MembersHolder>::box_type = boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >; boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type = long unsigned int; boost::geometry::index::detail::rtree::pack<MembersHolder>::parameters_type = boost::geometry::index::quadratic<16>; boost::geometry::index::detail::rtree::pack<MembersHolder>::translator_type = boost::geometry::index::detail::translator<boost::geometry::index::indexable<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, boost::geometry::index::equal_to<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > >; boost::geometry::index::detail::rtree::pack<MembersHolder>::allocators_type = boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/pack_create.hpp:204:40:   required from 'static boost::geometry::index::detail::rtree::pack<MembersHolder>::node_pointer boost::geometry::index::detail::rtree::pack<MembersHolder>::apply(InIt, InIt, boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type&, boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type&, const parameters_type&, const translator_type&, boost::geometry::index::detail::rtree::pack<MembersHolder>::allocators_type&) [with InIt = __gnu_cxx::__normal_iterator<const std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > >; MembersHolder = boost::geometry::index::rtree<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16> >::members_holder; boost::geometry::index::detail::rtree::pack<MembersHolder>::node_pointer = boost::variant<boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >*; boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type = long unsigned int; boost::geometry::index::detail::rtree::pack<MembersHolder>::parameters_type = boost::geometry::index::quadratic<16>; boost::geometry::index::detail::rtree::pack<MembersHolder>::translator_type = boost::geometry::index::detail::translator<boost::geometry::index::indexable<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, boost::geometry::index::equal_to<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > >; boost::geometry::index::detail::rtree::pack<MembersHolder>::allocators_type = boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/rtree.hpp:436:37:   required from 'boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::rtree(const Range&, const parameters_type&, const indexable_getter&, const value_equal&, const allocator_type&) [with Range = std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; Value = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>; Parameters = boost::geometry::index::quadratic<16>; IndexableGetter = boost::geometry::index::indexable<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; EqualTo = boost::geometry::index::equal_to<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; Allocator = boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::parameters_type = boost::geometry::index::quadratic<16>; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::indexable_getter = boost::geometry::index::indexable<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::value_equal = boost::geometry::index::equal_to<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::allocator_type = boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >]'  
prog.cc:16:56:   required from here  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray_detail.hpp:544:13: warning: 'void* memcpy(void*, const void*, size_t)' writing to an object of type 'struct std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>' with no trivial copy-assignment; use copy-assignment or copy-initialization instead [-Wclass-memaccess]  
     ::memcpy(boost::addressof(*pos), boost::addressof(v), sizeof(V));  
     ~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /opt/wandbox/gcc-8.3.0/include/c++/8.3.0/utility:70,  
                 from /opt/wandbox/gcc-8.3.0/include/c++/8.3.0/algorithm:60,  
                 from /opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/rtree.hpp:20,  
                 from prog.cc:1:  
/opt/wandbox/gcc-8.3.0/include/c++/8.3.0/bits/stl_pair.h:208:12: note: 'struct std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>' declared here  
     struct pair  
            ^~~~  
In file included from /opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray.hpp:41,  
                 from /opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/node/node.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/rtree.hpp:60,  
                 from prog.cc:1:  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray_detail.hpp: In instantiation of 'O boost::geometry::index::detail::varray_detail::uninitialized_copy_dispatch(I, I, O, const mpl_::bool_<true>&) [with I = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*; O = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*]':  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray_detail.hpp:262:39:   required from 'F boost::geometry::index::detail::varray_detail::uninitialized_copy(I, I, F) [with I = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*; F = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray_detail.hpp:423:43:   required from 'O boost::geometry::index::detail::varray_detail::uninitialized_move_if_noexcept_dispatch(I, I, O, const mpl_::bool_<false>&) [with I = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*; O = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray_detail.hpp:433:51:   required from 'O boost::geometry::index::detail::varray_detail::uninitialized_move_if_noexcept(I, I, O) [with I = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*; O = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray.hpp:1516:43:   required from 'void boost::geometry::index::detail::varray<Value, Capacity>::move_ctor_dispatch(boost::geometry::index::detail::varray<typename boost::geometry::index::detail::varray_detail::varray_traits<Value, Capacity>::value_type, C>&, boost::false_type) [with long unsigned int C = 17; Value = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>; long unsigned int Capacity = 17; typename boost::geometry::index::detail::varray_detail::varray_traits<Value, Capacity>::value_type = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>; boost::false_type = boost::integral_constant<bool, false>]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray.hpp:385:9:   required from 'boost::geometry::index::detail::varray<Value, Capacity>::varray(boost::geometry::index::detail::varray<Value, Capacity>&&) [with Value = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>; long unsigned int Capacity = 17]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/node/variant_static.hpp:35:8:   [ skipping 5 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/node/variant_dynamic.hpp:206:22:   required from 'static VariantPtr boost::geometry::index::detail::rtree::create_variant_node<VariantPtr, Node>::apply(AllocNode&) [with AllocNode = boost::container::new_allocator<boost::variant<boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> > >; VariantPtr = boost::variant<boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >*; Node = boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/node/variant_dynamic.hpp:258:17:   required from 'static typename Allocators::node_pointer boost::geometry::index::detail::rtree::create_node<Allocators, boost::geometry::index::detail::rtree::variant_leaf<Value, Parameters, Box, Allocators, Tag> >::apply(Allocators&) [with Allocators = boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>; Value = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>; Parameters = boost::geometry::index::quadratic<16>; Box = boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >; Tag = boost::geometry::index::detail::rtree::node_variant_static_tag; typename Allocators::node_pointer = boost::variant<boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >*]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/pack_create.hpp:287:78:   required from 'static boost::geometry::index::detail::rtree::pack<MembersHolder>::internal_element boost::geometry::index::detail::rtree::pack<MembersHolder>::per_level(EIt, EIt, const box_type&, boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type, const boost::geometry::index::detail::rtree::pack<MembersHolder>::subtree_elements_counts&, const parameters_type&, const translator_type&, boost::geometry::index::detail::rtree::pack<MembersHolder>::allocators_type&) [with EIt = __gnu_cxx::__normal_iterator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, __gnu_cxx::__normal_iterator<const std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > > >*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, __gnu_cxx::__normal_iterator<const std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > > >, std::allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, __gnu_cxx::__normal_iterator<const std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > > > > > >; MembersHolder = boost::geometry::index::rtree<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16> >::members_holder; boost::geometry::index::detail::rtree::pack<MembersHolder>::internal_element = boost::geometry::index::detail::rtree::ptr_pair<boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::variant<boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >*>; boost::geometry::index::detail::rtree::pack<MembersHolder>::box_type = boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >; boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type = long unsigned int; boost::geometry::index::detail::rtree::pack<MembersHolder>::parameters_type = boost::geometry::index::quadratic<16>; boost::geometry::index::detail::rtree::pack<MembersHolder>::translator_type = boost::geometry::index::detail::translator<boost::geometry::index::indexable<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, boost::geometry::index::equal_to<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > >; boost::geometry::index::detail::rtree::pack<MembersHolder>::allocators_type = boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/rtree/pack_create.hpp:204:40:   required from 'static boost::geometry::index::detail::rtree::pack<MembersHolder>::node_pointer boost::geometry::index::detail::rtree::pack<MembersHolder>::apply(InIt, InIt, boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type&, boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type&, const parameters_type&, const translator_type&, boost::geometry::index::detail::rtree::pack<MembersHolder>::allocators_type&) [with InIt = __gnu_cxx::__normal_iterator<const std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>*, std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > >; MembersHolder = boost::geometry::index::rtree<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16> >::members_holder; boost::geometry::index::detail::rtree::pack<MembersHolder>::node_pointer = boost::variant<boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16, 4>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >*; boost::geometry::index::detail::rtree::pack<MembersHolder>::size_type = long unsigned int; boost::geometry::index::detail::rtree::pack<MembersHolder>::parameters_type = boost::geometry::index::quadratic<16>; boost::geometry::index::detail::rtree::pack<MembersHolder>::translator_type = boost::geometry::index::detail::translator<boost::geometry::index::indexable<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, boost::geometry::index::equal_to<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> > >; boost::geometry::index::detail::rtree::pack<MembersHolder>::allocators_type = boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >, std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>, boost::geometry::index::quadratic<16>, boost::geometry::model::box<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/rtree.hpp:436:37:   required from 'boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::rtree(const Range&, const parameters_type&, const indexable_getter&, const value_equal&, const allocator_type&) [with Range = std::vector<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; Value = std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>; Parameters = boost::geometry::index::quadratic<16>; IndexableGetter = boost::geometry::index::indexable<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; EqualTo = boost::geometry::index::equal_to<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; Allocator = boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::parameters_type = boost::geometry::index::quadratic<16>; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::indexable_getter = boost::geometry::index::indexable<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::value_equal = boost::geometry::index::equal_to<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >; boost::geometry::index::rtree<Value, Options, IndexableGetter, EqualTo, Allocator>::allocator_type = boost::container::new_allocator<std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int> >]'  
prog.cc:16:56:   required from here  
/opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/detail/varray_detail.hpp:237:13: warning: 'void* memcpy(void*, const void*, size_t)' writing to an object of type 'struct std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>' with no trivial copy-assignment; use copy-assignment or copy-initialization instead [-Wclass-memaccess]  
     ::memcpy(boost::addressof(*dst), boost::addressof(*first), sizeof(value_type) * d);  
     ~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /opt/wandbox/gcc-8.3.0/include/c++/8.3.0/utility:70,  
                 from /opt/wandbox/gcc-8.3.0/include/c++/8.3.0/algorithm:60,  
                 from /opt/wandbox/boost-1.73.0/gcc-8.3.0/include/boost/geometry/index/rtree.hpp:20,  
                 from prog.cc:1:  
/opt/wandbox/gcc-8.3.0/include/c++/8.3.0/bits/stl_pair.h:208:12: note: 'struct std::pair<boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>, long unsigned int>' declared here  
     struct pair  
            ^~~~  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-04-30 17:33:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-621997470  

Thanks for the report. It seems that this warning is generated by GCC >= 8.  
  
AFAIU this is a false positive. The intention is to copy-construct the value here [1], not to assign to it.  
  
`boost::has_trivial_copy<T>::value` is checked prior to that and for `std::pair<point, std::size_t>` the value is `true`. According to the documentation [2]:  
  
> If a type has a trivial copy-constructor then the constructor has the same effect as copying the bits of one object to the other: calls to the constructor can be safely replaced with a call to memcpy.  
  
I'll think about suppressing it. The simplest thing to do would probably be to also check `boost::has_trivial_assign<T>::value`. But AFAIU this check is not required here, it would only be used to silence GCC and I don't like that.  
  
@mloskot Do you have any thoughts?  
  
[1] https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/varray_detail.hpp#L544  
[2] https://www.boost.org/doc/libs/1_73_0/libs/type_traits/doc/html/boost_typetraits/reference/has_trivial_copy.html

---

## Comment 2

> Username: tower120  
> Created at: 2020-05-01 06:54:47 UTC  
> Updated at: 2020-05-01 06:57:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-622274741  

The thing is, `std::pair` is not trivially_copyable:  
  
https://wandbox.org/permlink/Dxls2iQI2kCSxFrq  
  
Nor, trivially assignable to self:  
https://wandbox.org/permlink/az3VKnZ8P1YxyWGb

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-05-01 22:13:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-622588610  

Yes, I think you are correct pointing it out and that it is not false positive. The standard says that in order to copy a value by copying the undelying bytes the type has to be trivially copyable and this means trivial copy ctors, trivial copy assignments and trivial dtor. AFAIU the older standard (03) was even stricter as it required the type to be POD. So AFAIU everything else is UB.  
  
Now I'm wondering am I missing something, misinterpreting the standard or is Boost.TypeTraits documentation wrong or not mentioning all of the other requirements?  
  
Anyway, for now I classified this issue as a bug and proposed a fix here: https://github.com/boostorg/geometry/pull/710

---

## Comment 4

> Username: tower120  
> Created at: 2020-05-02 07:10:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-622769602  

Frankly speaking, I don't think that you'll see any performance win by replacing "=" with memcpy...

---

## Comment 5

> Username: awulkiew  
> Created at: 2020-05-02 13:25:29 UTC  
> Updated at: 2020-05-02 13:27:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-622953432  

> I don't think that you'll see any performance win  
  
Yes, this it probable. Yesterday I tested the performence of `varray::push_back` with VS2017 and it was the same for placement new vs `memcpy` for copy-construction. However I saw benchmarks on the internet where the performance improvement is there. As usual this may depend on the specific use case, compiler, optimization level, OS or hardware. Furthermore in `push_back` only one value is copied this way, while performing a copy of multiple values the difference could be bigger, etc. We may guess all day long. I would be sure if I made extensive benchmarking. I don't have time for that now though. Have you done such testing? If yes, could you share the results? If not, what is your assessment based on?

---

## Comment 6

> Username: tower120  
> Created at: 2020-05-02 16:03:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-622975984  

I didn't dig into guts of `varray`, and don't know how its work.  
My reasoning is - if compiler see that type is bitwise-copyable, why it should generate suboptimal code, which differs from memcpy?

---

## Comment 7

> Username: tower120  
> Created at: 2020-05-02 16:10:14 UTC  
> Updated at: 2020-05-02 16:11:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-622976931  

Another thing is "copy elision"... If std::memcpy implemented as underlying call to OS API, compiler will not be able to elide it, in contrast to no-op with "copy elision".

---

## Comment 8

> Username: awulkiew  
> Created at: 2020-05-02 16:57:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-622983288  

I have nothing against changing it after seeing a proof that this is indeed the case with several compilers. Or at least if you or anyone else discovered that varray might be faster in your/his/her specific case. I'd gladly review a PR with such change.

---

## Comment 9

> Username: awulkiew  
> Created at: 2020-05-13 15:02:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-628051121  

Thanks again for this report. I'll probably go back to this later and as you suggested remove `memcpy` entirely.

---

## Comment 10

> Username: tower120  
> Created at: 2020-05-13 15:14:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-628058472  

I did not actually suggest to completely remove it...  
I just said that I would expect them to have exact performance characteristics....

---

## Comment 11

> Username: awulkiew  
> Created at: 2020-05-13 16:34:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/709#issuecomment-628105672  

In some cases you expect them to have even better performance and your reasoning makes sense. :)  
  
Furthermore I did some quick benchmarking and indeed there is no difference and in some cases the performance may be greater (e.g. raw assignment seems to be faster for PoD types than placement new or memcpy). No worries, I'll test more before changing anything.  
  
I'm just letting you know that at some point I'll probably look into this deeper and that I'm taking your legitimate remarks into account. If you'd like to dig into this feel free to propose a PR too.
