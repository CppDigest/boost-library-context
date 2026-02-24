# #105 Remove reference from argument_type [Merged]

> Username: danieljames  
> Created at: 2018-03-31 13:38:44 UTC  
> Updated at: 2018-04-13 12:22:13 UTC  
> Merged at: 2018-04-13 12:22:13 UTC  
> Closed at: 2018-04-13 12:22:13 UTC  
> Url: https://github.com/boostorg/graph/pull/105  

The reference was added in pull request #89. It causes a regression in `transitive_closure_test` compiled with gcc 4.4 in C++98 mode.  
  
The error message from: https://www.boost.org/development/tests/develop/developer/output/teeks99-02-dg4-4-98-Docker-64on64-boost-bin-v2-libs-graph-test-transitive_closure_test-test-gcc-4-4~c++98-debug.html  
  
    In file included from /usr/include/c++/4.4/string:50,  
                     from /usr/include/c++/4.4/bits/locale_classes.h:42,  
                     from /usr/include/c++/4.4/bits/ios_base.h:43,  
                     from /usr/include/c++/4.4/ios:43,  
                     from /usr/include/c++/4.4/ostream:40,  
                     from /usr/include/c++/4.4/iostream:40,  
                     from ../libs/graph/test/transitive_closure_test.cpp:7:  
    /usr/include/c++/4.4/bits/stl_function.h: In instantiation of 'std::unary_negate<boost::detail::subscript_t<std::vector<long unsigned int, std::allocator<long unsigned int> >, long unsigned int, long unsigned int> >':  
    ../boost/graph/transitive_closure.hpp:157:   instantiated from 'void boost::transitive_closure(const Graph&, GraphTC&, G_to_TC_VertexMap, VertexIndexMap) [with Graph = std::vector<std::vector<int, std::allocator<int> >, std::allocator<std::vector<int, std::allocator<int> > > >, GraphTC = std::vector<std::vector<int, std::allocator<int> >, std::allocator<std::vector<int, std::allocator<int> > > >, G_to_TC_VertexMap = boost::iterator_property_map<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >, boost::typed_identity_property_map<long unsigned int>, int, int&>, VertexIndexMap = boost::typed_identity_property_map<long unsigned int>]'  
    ../boost/graph/transitive_closure.hpp:285:   instantiated from 'void boost::detail::transitive_closure_dispatch(const Graph&, GraphTC&, G_to_TC_VertexMap, VertexIndexMap) [with Graph = std::vector<std::vector<int, std::allocator<int> >, std::allocator<std::vector<int, std::allocator<int> > > >, GraphTC = std::vector<std::vector<int, std::allocator<int> >, std::allocator<std::vector<int, std::allocator<int> > > >, G_to_TC_VertexMap = boost::param_not_found, VertexIndexMap = boost::typed_identity_property_map<long unsigned int>]'  
    ../boost/graph/transitive_closure.hpp:300:   instantiated from 'void boost::transitive_closure(const Graph&, GraphTC&, const boost::bgl_named_params<P, T, R>&) [with Graph = std::vector<std::vector<int, std::allocator<int> >, std::allocator<std::vector<int, std::allocator<int> > > >, GraphTC = std::vector<std::vector<int, std::allocator<int> >, std::allocator<std::vector<int, std::allocator<int> > > >, P = boost::typed_identity_property_map<long unsigned int>, T = boost::vertex_index_t, R = boost::no_property]'  
    ../libs/graph/test/transitive_closure_test.cpp:120:   instantiated from here  
    /usr/include/c++/4.4/bits/stl_function.h:357: error: forming reference to reference type 'long unsigned int&'

---
