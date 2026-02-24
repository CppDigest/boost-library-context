# #27 - Large number of warnings about deprecated features [Open]

> Username: jzmaddock  
> Created at: 2021-05-31 18:04:21 UTC  
> Updated at: 2021-05-31 18:04:21 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/27  

Specifically:  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/graph_parallel/test/adjlist_build_test-2.test/gcc-10/debug/cxxstd-17-iso/threading-multi/visibility-hidden/adjlist_build_test.o  
In file included from ../../../boost/smart_ptr/detail/sp_thread_sleep.hpp:22,  
                 from ../../../boost/smart_ptr/detail/yield_k.hpp:23,  
                 from ../../../boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14,  
                 from ../../../boost/smart_ptr/detail/spinlock.hpp:42,  
                 from ../../../boost/smart_ptr/detail/spinlock_pool.hpp:25,  
                 from ../../../boost/smart_ptr/shared_ptr.hpp:29,  
                 from ../../../boost/shared_ptr.hpp:17,  
                 from ../../../boost/property_map/parallel/distributed_property_map.hpp:22,  
                 from ../../../boost/property_map/parallel/parallel_property_maps.hpp:30,  
                 from ../../../boost/property_map/property_map.hpp:602,  
                 from ../../../boost/graph/adjacency_list.hpp:27,  
                 from ../../../boost/graph/distributed/adjacency_list.hpp:18,  
                 from adjlist_build_test.cpp:10:  
../../../boost/config/pragma_message.hpp:24:34: note: ‘#pragma message: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.’  
   24 | # define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
      |                                  ^~~~~~~  
../../../boost/bind.hpp:36:1: note: in expansion of macro ‘BOOST_PRAGMA_MESSAGE’  
   36 | BOOST_PRAGMA_MESSAGE(  
      | ^~~~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/graph/distributed/adjacency_list.hpp:53,  
                 from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/named_graph.hpp: In destructor ‘boost::graph::distributed::named_graph<Graph, Vertex, Edge, Config>::lazy_add_vertex::~lazy_add_vertex()’:  
../../../boost/graph/distributed/named_graph.hpp:379:25: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
  379 |   if (committed || std::uncaught_exception())  
      |                         ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/graph/distributed/adjacency_list.hpp:53,  
                 from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/named_graph.hpp:379:44: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
  379 |   if (committed || std::uncaught_exception())  
      |                                            ^  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/graph/distributed/adjacency_list.hpp:53,  
                 from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/named_graph.hpp: In destructor ‘boost::graph::distributed::named_graph<Graph, Vertex, Edge, Config>::lazy_add_edge::~lazy_add_edge()’:  
../../../boost/graph/distributed/named_graph.hpp:489:25: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
  489 |   if (committed || std::uncaught_exception())  
      |                         ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/graph/distributed/adjacency_list.hpp:53,  
                 from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/named_graph.hpp:489:44: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
  489 |   if (committed || std::uncaught_exception())  
      |                                            ^  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/graph/distributed/adjacency_list.hpp:53,  
                 from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/named_graph.hpp: In destructor ‘boost::graph::distributed::named_graph<Graph, Vertex, Edge, Config>::lazy_add_edge_with_property::~lazy_add_edge_with_property()’:  
../../../boost/graph/distributed/named_graph.hpp:685:25: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
  685 |   if (committed || std::uncaught_exception())  
      |                         ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/graph/distributed/adjacency_list.hpp:53,  
                 from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/named_graph.hpp:685:44: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
  685 |   if (committed || std::uncaught_exception())  
      |                                            ^  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp: In destructor ‘boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::lazy_add_vertex_with_property::~lazy_add_vertex_with_property()’:  
../../../boost/graph/distributed/adjacency_list.hpp:2289:27: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2289 |     if (committed || std::uncaught_exception())  
      |                           ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp:2289:46: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2289 |     if (committed || std::uncaught_exception())  
      |                                              ^  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp: In destructor ‘boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::lazy_add_edge::~lazy_add_edge()’:  
../../../boost/graph/distributed/adjacency_list.hpp:2402:27: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2402 |     if (committed || std::uncaught_exception())  
      |                           ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp:2402:46: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2402 |     if (committed || std::uncaught_exception())  
      |                                              ^  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp: In destructor ‘boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::lazy_add_edge_with_property::~lazy_add_edge_with_property()’:  
../../../boost/graph/distributed/adjacency_list.hpp:2587:33: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2587 |     if (this->committed || std::uncaught_exception())  
      |                                 ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp:2587:52: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2587 |     if (this->committed || std::uncaught_exception())  
      |                                                    ^  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/smart_ptr/detail/sp_thread_sleep.hpp:22,  
                 from ../../../boost/smart_ptr/detail/yield_k.hpp:23,  
                 from ../../../boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14,  
                 from ../../../boost/smart_ptr/detail/spinlock.hpp:42,  
                 from ../../../boost/smart_ptr/detail/spinlock_pool.hpp:25,  
                 from ../../../boost/smart_ptr/shared_ptr.hpp:29,  
                 from ../../../boost/shared_ptr.hpp:17,  
                 from ../../../boost/property_map/parallel/distributed_property_map.hpp:22,  
                 from ../../../boost/property_map/parallel/parallel_property_maps.hpp:30,  
                 from ../../../boost/property_map/property_map.hpp:602,  
                 from ../../../boost/graph/adjacency_list.hpp:27,  
                 from ../../../boost/graph/distributed/adjacency_list.hpp:18,  
                 from adjlist_build_test.cpp:10:  
../../../boost/test/minimal.hpp: At global scope:  
../../../boost/config/pragma_message.hpp:24:34: note: ‘#pragma message: This header is deprecated. Use <boost/test/included/unit_test.hpp> instead.’  
   24 | # define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
      |                                  ^~~~~~~  
../../../boost/config/header_deprecated.hpp:23:37: note: in expansion of macro ‘BOOST_PRAGMA_MESSAGE’  
   23 | # define BOOST_HEADER_DEPRECATED(a) BOOST_PRAGMA_MESSAGE("This header is deprecated. Use " a " instead.")  
      |                                     ^~~~~~~~~~~~~~~~~~~~  
../../../boost/test/minimal.hpp:35:1: note: in expansion of macro ‘BOOST_HEADER_DEPRECATED’  
   35 | BOOST_HEADER_DEPRECATED( "<boost/test/included/unit_test.hpp>" )  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp: In instantiation of ‘boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::lazy_add_edge::~lazy_add_edge() [with OutEdgeListS = boost::listS; ProcessGroup = boost::graph::distributed::mpi_process_group; InVertexListS = boost::vecS; InDistribution = boost::defaultS; DirectedS = boost::bidirectionalS; VertexProperty = boost::no_property; EdgeProperty = boost::no_property; GraphProperty = boost::no_property; EdgeListS = boost::listS]’:  
adjlist_build_test.cpp:90:75:   required from here  
../../../boost/graph/distributed/adjacency_list.hpp:2402:45: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2402 |     if (committed || std::uncaught_exception())  
      |                      ~~~~~~~~~~~~~~~~~~~~~~~^~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp:2402:45: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2402 |     if (committed || std::uncaught_exception())  
      |                      ~~~~~~~~~~~~~~~~~~~~~~~^~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp:2402:45: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2402 |     if (committed || std::uncaught_exception())  
      |                      ~~~~~~~~~~~~~~~~~~~~~~~^~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp: In instantiation of ‘boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::lazy_add_edge_with_property::~lazy_add_edge_with_property() [with OutEdgeListS = boost::listS; ProcessGroup = boost::graph::distributed::mpi_process_group; InVertexListS = boost::vecS; InDistribution = boost::defaultS; DirectedS = boost::bidirectionalS; VertexProperty = boost::no_property; EdgeProperty = boost::no_property; GraphProperty = boost::no_property; EdgeListS = boost::listS]’:  
../../../boost/graph/distributed/adjlist/handlers.hpp:107:11:   required from ‘void boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::handle_add_edge_with_property(int, int, const msg_add_edge_with_property_data&, boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::trigger_receive_context) [with OutEdgeListS = boost::listS; ProcessGroup = boost::graph::distributed::mpi_process_group; InVertexListS = boost::vecS; InDistribution = boost::defaultS; DirectedS = boost::bidirectionalS; VertexProperty = boost::no_property; EdgeProperty = boost::no_property; GraphProperty = boost::no_property; EdgeListS = boost::listS; boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::msg_add_edge_with_property_data = boost::detail::parallel::msg_add_edge_with_property_data<boost::detail::parallel::global_descriptor<long unsigned int>, long unsigned int, boost::no_property>; boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::trigger_receive_context = boost::parallel::trigger_receive_context]’  
../../../boost/graph/distributed/adjlist/handlers.hpp:38:18:   required from ‘void boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::setup_triggers() [with OutEdgeListS = boost::listS; ProcessGroup = boost::graph::distributed::mpi_process_group; InVertexListS = boost::vecS; InDistribution = boost::defaultS; DirectedS = boost::bidirectionalS; VertexProperty = boost::no_property; EdgeProperty = boost::no_property; GraphProperty = boost::no_property; EdgeListS = boost::listS]’  
../../../boost/graph/distributed/adjacency_list.hpp:1585:7:   required from ‘boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::adjacency_list(EdgeIterator, EdgeIterator, boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::vertices_size_type, const ProcessGroup&, const GraphProperty&) [with EdgeIterator = boost::erdos_renyi_iterator<boost::random::linear_congruential_engine<unsigned int, 48271, 0, 2147483647>, boost::adjacency_list<boost::listS, boost::distributedS<boost::graph::distributed::mpi_process_group, boost::vecS>, boost::bidirectionalS> >; OutEdgeListS = boost::listS; ProcessGroup = boost::graph::distributed::mpi_process_group; InVertexListS = boost::vecS; InDistribution = boost::defaultS; DirectedS = boost::bidirectionalS; VertexProperty = boost::no_property; EdgeProperty = boost::no_property; GraphProperty = boost::no_property; EdgeListS = boost::listS; boost::adjacency_list<OutEdgeListS, boost::distributedS<ProcessGroup, InVertexListS, InDistribution>, DirectedS, VertexProperty, EdgeProperty, GraphProperty, EdgeListS>::vertices_size_type = long unsigned int]’  
adjlist_build_test.cpp:71:47:   required from here  
../../../boost/graph/distributed/adjacency_list.hpp:2587:51: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2587 |     if (this->committed || std::uncaught_exception())  
      |                            ~~~~~~~~~~~~~~~~~~~~~~~^~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp:2587:51: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2587 |     if (this->committed || std::uncaught_exception())  
      |                            ~~~~~~~~~~~~~~~~~~~~~~~^~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
In file included from adjlist_build_test.cpp:10:  
../../../boost/graph/distributed/adjacency_list.hpp:2587:51: warning: ‘bool std::uncaught_exception()’ is deprecated [-Wdeprecated-declarations]  
 2587 |     if (this->committed || std::uncaught_exception())  
      |                            ~~~~~~~~~~~~~~~~~~~~~~~^~  
In file included from ../../../boost/throw_exception.hpp:27,  
                 from adjlist_build_test.cpp:9:  
/usr/include/c++/10/exception:106:8: note: declared here  
  106 |   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
      |        ^~~~~~~~~~~~~~~~~~  
```
