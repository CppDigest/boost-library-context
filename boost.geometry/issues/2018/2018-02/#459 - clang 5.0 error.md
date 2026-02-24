# #459 - clang 5.0 error [Closed]

> Username: skn123  
> Created at: 2018-02-13 08:21:39 UTC  
> Updated at: 2018-02-15 00:04:29 UTC  
> Closed at: 2018-02-15 00:04:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/459  

``/usr/local/include/boost/geometry/index/detail/rtree/node/variant_dynamic.hpp:188:61: error:   
      no member named 'to_address' in  
      'boost::pointer_traits<boost::variant<boost::geometry::index::detail::rtree::variant_leaf<std::pair<boost::geometry::model::box<boost::geometry::model::point<float,  
      2, boost::geometry::cs::cartesian> >, std::pair<double, double> >,  
      boost::geometry::index::quadratic<4, 2>,  
      boost::geometry::model::box<boost::geometry::model::point<float, 2,  
      boost::geometry::cs::cartesian> >,  
      boost::geometry::index::detail::rtree::allocators<std::allocator<std::pair<boost::geometry::model::box<boost::geometry::model::point<float,  
      2, boost::geometry::cs::cartesian> >, std::pair<double, double> > >,  
      std::pair<boost::geometry::model::box<boost::geometry::model::point<float,  
      2, boost::geometry::cs::cartesian> >, std::pair<double, double> >,  
      boost::geometry::index::quadratic<4, 2>,  
      boost::geometry::model::box<boost::geometry::model::point<float, 2,  
      boost::geometry::cs::cartesian> >,  
      boost::geometry::index::detail::rtree::node_variant_static_tag>,  
      boost::geometry::index::detail::rtree::node_variant_static_tag>,  
      boost::geometry::index::detail::rtree::variant_internal_node<std::pair<boost::geometry::model::box<boost::geometry::model::point<float,  
      2, boost::geometry::cs::cartesian> >, std::pair<double, double> >,  
      boost::geometry::index::quadratic<4, 2>,  
      boost::geometry::model::box<boost::geometry::model::point<float, 2,  
      boost::geometry::cs::cartesian> >,  
      boost::geometry::index::detail::rtree::allocators<std::allocator<std::pair<boost::geometry::model::box<boost::geometry::model::point<float,  
      2, boost::geometry::cs::cartesian> >, std::pair<double, double> > >,  
      std::pair<boost::geometry::model::box<boost::geometry::model::point<float,  
      2, boost::geometry::cs::cartesian> >, std::pair<double, double> >,  
      boost::geometry::index::quadratic<4, 2>,  
      boost::geometry::model::box<boost::geometry::model::point<float, 2,  
      boost::geometry::cs::cartesian> >,  
      boost::geometry::index::detail::rtree::node_variant_static_tag>,  
      boost::geometry::index::detail::rtree::node_variant_static_tag> > *>'  
        Al::construct(alloc_node, boost::pointer_traits<P>::to_address(p...  
                                  ~~~~~~~~~~~~~~~~~~~~~~~~~~^  
``

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-02-13 11:07:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/459#issuecomment-365233335  

Which version? Master branch maybe?

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-02-15 00:03:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/459#issuecomment-365786198  

I'm assuming it's master branch. Should be fixed now.
