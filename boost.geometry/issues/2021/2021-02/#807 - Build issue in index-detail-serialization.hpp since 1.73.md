# #807 - Build issue in index/detail/serialization.hpp since 1.73 [Closed]

> Username: tristan0x  
> Created at: 2021-02-13 18:09:28 UTC  
> Updated at: 2021-02-17 16:02:44 UTC  
> Closed at: 2021-02-17 16:02:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/807  

```  
In file included from /path/to/boost-1.73.0-ell6js/include/boost/geometry/index/rtree.hpp:90,  
                 from /path/to/my-project/include/spatial_index/index.hpp:13,  
                 from /path/to/my-project/python/bind_common.hpp:3,  
                 from /path/to/my-project/python/rtree_index.hpp:3,  
                 from /path/to/my-project/python/py_bindings.cpp:2:  
/path/to/boost-1.73.0-ell6js/include/boost/geometry/index/detail/serialization.hpp: In static member function 'static boost::geometry::index::detail::rtree::load<MembersHolder>::node_pointer boost::geometry::index::detail::rtree::load<MembersHolder>::raw_apply(Archive&, unsigned int, boost::geometry::index::detail::rtree::load<MembersHolder>::size_type, boost::geometry::index::detail::rtree::load<MembersHolder>::size_type&, const parameters_type&, const translator_type&, boost::geometry::index::detail::rtree::load<MembersHolder>::allocators_type&, boost::geometry::index::detail::rtree::load<MembersHolder>::size_type)':  
/path/to/boost-1.73.0-ell6js/include/boost/geometry/index/detail/serialization.hpp:399:49: error: 'Allocators' was not declared in this scope; did you mean 'allocators'?  
  399 |             node_pointer n = rtree::create_node<Allocators, internal_node>::apply(allocators);              // MAY THROW (A)  
      |                                                 ^~~~~~~~~~  
      |                                                 allocators  
compilation terminated due to -Wfatal-errors.  
```  
  
Build issue is independent of the compiler.
