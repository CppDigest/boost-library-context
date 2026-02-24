# #565 - Compile-time constructed rtree with MaxElements >= 2M segFaults [Closed]

> Username: amrobbins  
> Created at: 2019-03-06 00:01:49 UTC  
> Updated at: 2019-04-08 01:54:01 UTC  
> Closed at: 2019-04-08 01:53:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/565  

I'm trying to create an rtree that contains about 50M points, and want to use a memory mapped file for this. Because of this I'm creating a compile-time constructed rtree. I have found that whenever I create my rtree with a size much bigger than 1M entries, I get a segfault when I try to insert an element into the tree. For example this happens when I create one rtree of size 2M entries, but interestingly it doesn't happen when I create two rtrees each of 1M entries. I pasted two examples below, the first one segfaults, the second one works without segfaulting:  
  
Example that segfaults:  
------------------------------------------------  
#include < iostream >  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point.hpp>  
#include <boost/geometry/index/rtree.hpp>  
  
namespace bg = boost::geometry;  
namespace bgm = bg::model;  
namespace bgi = bg::index;  
  
int main()  
{  
    typedef bgm::point<float, 2, bg::cs::cartesian> point_t;  
  
    bgi::rtree<point_t, bgi::quadratic<2000000>, bgi::indexable<point_t>, bgi::equal_to<point_t>> rtree0;  
  
    rtree0.insert(point_t(3.0, 3.0));  
  
    std::cout << "0: "<< rtree0.size() << std::endl;  
    return 0;  
}  
  
  
// Ubuntu 18.04, Intel x64, boost 1.69.0  
//  
// $ g++ --version  
// g++ (Ubuntu 7.3.0-27ubuntu1~18.04) 7.3.0  
//  
// $ g++ -I /usr/local/boost_1_69_0 -std=c++11 -pthread notWorking.cpp -O0 -g -o notWorking  
// $ ./notWorking  
// Segmentation fault (core dumped)  
// $ gdb ./notWorking  
// ...  
// (gdb) bt  
// #0  0x0000555555557421 in boost::geometry::index::detail::rtree::create_variant_node<boost::variant<boost::geometry::index::detail::rtree::variant_leaf<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >*, boost::geometry::index::detail::rtree::variant_leaf<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >::apply<boost::container::new_allocator<boost::variant<boost::geometry::index::detail::rtree::variant_leaf<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_internal_node<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> > > > (alloc_node=<error reading variable: Cannot access memory at address 0x7fffff0bb868>)  
//     at /usr/local/boost_1_69_0/boost/geometry/index/detail/rtree/node/variant_dynamic.hpp:194  
// #1  0x0000555555557208 in boost::geometry::index::detail::rtree::create_node<boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::variant_leaf<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::model::box<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::detail::rtree::node_variant_static_tag>, boost::geometry::index::detail::rtree::node_variant_static_tag> >::apply (allocators=...) at /usr/local/boost_1_69_0/boost/geometry/index/detail/rtree/node/variant_dynamic.hpp:258  
// #2  0x0000555555556e06 in boost::geometry::index::rtree<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::index::indexable<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::equal_to<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> > >::raw_create (this=0x7fffffffdda0) at /usr/local/boost_1_69_0/boost/geometry/index/rtree.hpp:1510  
// #3  0x000055555555684d in boost::geometry::index::rtree<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian>, boost::geometry::index::quadratic<2000000ul, 600000ul>, boost::geometry::index::indexable<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::geometry::index::equal_to<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> >, boost::container::new_allocator<boost::geometry::model::point<float, 2ul, boost::geometry::cs::cartesian> > >::insert (this=0x7fffffffdda0, value=...) at /usr/local/boost_1_69_0/boost/geometry/index/rtree.hpp:581  
// #4  0x0000555555555fae in main () at notWorking.cpp:17  
  
  
  
Example that doesn't segfault:  
------------------------------------------------  
#include < iostream >  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point.hpp>  
#include <boost/geometry/index/rtree.hpp>  
  
namespace bg = boost::geometry;  
namespace bgm = bg::model;  
namespace bgi = bg::index;  
  
int main()  
{  
    typedef bgm::point<float, 2, bg::cs::cartesian> point_t;  
  
    bgi::rtree<point_t, bgi::quadratic<1000000>, bgi::indexable<point_t>, bgi::equal_to<point_t>> rtree0;  
    bgi::rtree<point_t, bgi::quadratic<1000000>, bgi::indexable<point_t>, bgi::equal_to<point_t>> rtree1;  
  
    rtree0.insert(point_t(3.0, 3.0));  
    rtree1.insert(point_t(3.0, 3.0));  
  
    std::cout << "0: "<< rtree0.size() << std::endl;  
    std::cout << "1: "<< rtree1.size() << std::endl;  
    return 0;  
}  
  
  
// Ubuntu 18.04, Intel x64, boost 1.69.0  
//  
// $ g++ --version  
// g++ (Ubuntu 7.3.0-27ubuntu1~18.04) 7.3.0  
//  
// $ g++ -I /usr/local/boost_1_69_0 -std=c++11 -pthread working.cpp -O0 -g -o working  
// $ ./working  
// 0: 1  
// 1: 1  
// $

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-03-06 01:01:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/565#issuecomment-469922739  

In the examples you pasted you're creating R-trees with **nodes** containing 2M or 1M elements, not the **R-tree** containing 2M or 1M elements. My guess is that this is not what you wanted to do. Am I correct?  
  
Still, it's interesting why does the segfault happen exactly.

---

## Comment 2

> Username: amrobbins  
> Created at: 2019-03-06 22:45:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/565#issuecomment-470307976  

Yes, you're right, I thought that parameter was the total capacity of the rtree.

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-04-08 01:52:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/565#issuecomment-480656561  

I'll just assume that this has something to do with the max size of an object.
