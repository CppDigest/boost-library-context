# #106 Qualify call to as_const [Closed]

> Username: danieljames  
> Created at: 2018-03-31 13:40:09 UTC  
> Updated at: 2018-04-04 18:17:33 UTC  
> Closed at: 2018-04-04 18:17:33 UTC  
> Url: https://github.com/boostorg/graph/pull/106  

`std::as_const` was added to C++17 which makes the call ambiguous.  
  
You can see the error at https://www.boost.org/development/tests/develop/developer/output/teeks99-02-dc5-1z-lc-Docker-64on64-boost-bin-v2-libs-graph-test-test_graphs-test-clang-linux-5-0~c++1z~lc-debug.html  
  
    In file included from ../libs/graph/test/test_graphs.cpp:18:  
    In file included from ../libs/graph/test/test_graph.hpp:95:  
    ../libs/graph/test/test_properties.hpp:24:28: error: call to 'as_const' is ambiguous  
    GraphBundle const& cb1 = as_const(g)[graph_bundle];  
                            ^~~~~~~~  
    ../libs/graph/test/test_properties.hpp:120:3: note: in instantiation of function template specialization 'test_graph_bundle<boost::adjacency_matrix<boost::directedS, VertexBundle, EdgeBundle, GraphBundle, std::__1::allocator<bool> > >' requested here  
    test_graph_bundle(g, graph_bundled);  
    ^  
    ../libs/graph/test/test_graph.hpp:133:5: note: in instantiation of function template specialization 'test_properties<boost::adjacency_matrix<boost::directedS, VertexBundle, EdgeBundle, GraphBundle, std::__1::allocator<bool> >, std::__1::vector<unsigned long, std::__1::allocator<unsigned long> > >' requested here  
        test_properties(g, verts);  
        ^  
    ../libs/graph/test/test_graphs.cpp:132:5: note: in instantiation of function template specialization 'test_graph<boost::adjacency_matrix<boost::directedS, VertexBundle, EdgeBundle, GraphBundle, std::__1::allocator<bool> > >' requested here  
        test_graph(g);  
        ^  
    /usr/include/c++/v1/utility:291:50: note: candidate function [with _Tp = boost::adjacency_matrix<boost::directedS, VertexBundle, EdgeBundle, GraphBundle, std::__1::allocator<bool> >]  
    template <class _Tp> constexpr add_const_t<_Tp>& as_const(_Tp& __t) noexcept { return __t; }  
                                                    ^  
    ../libs/graph/test/test_properties.hpp:12:31: note: candidate function [with T = boost::adjacency_matrix<boost::directedS, VertexBundle, EdgeBundle, GraphBundle, std::__1::allocator<bool> >]  
    template<typename T> T const& as_const(T& x) { return x; }

---

## Comment 1

> Username: danieljames  
> Created_at: 2018-04-04 18:17:33 UTC  
> Url: https://github.com/boostorg/graph/pull/106#issuecomment-378696156  

Sorry, duplicate of #85.

---
