# #64 example: Build all the examples [Merged]

> Username: murraycu  
> Created at: 2016-05-02 10:06:44 UTC  
> Updated at: 2016-11-05 22:43:45 UTC  
> Merged at: 2016-10-31 19:20:27 UTC  
> Closed at: 2016-10-31 19:20:27 UTC  
> Url: https://github.com/boostorg/graph/pull/64  

There are many .cpp example files in Boost Graph's examples/ directory. These were not built when running bjam in that directory. That makes it harder to maintain that example code.  
  
<!-- Reviewable:start -->  
  
---  
  
This change is [<img src="https://reviewable.io/review_button.svg" height="35" align="absmiddle" alt="Reviewable"/>](https://reviewable.io/reviews/boostorg/graph/64)  
  
<!-- Reviewable:end -->

---

## Comment 1

> Username: murraycu  
> Created_at: 2016-05-03 12:24:56 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-216511972  

These commits let most of the examples now build, but I don't yet know enough about the boost graph API to fix the examples that use GraphViz. For instance, even if I replace the now-removed GraphViz type as suggested here:  
https://svn.boost.org/trac/boost/ticket/4762  
  
  typedef boost::adjacency_list<vecS, vecS, directedS,  
                              property<vertex_name_t, std::string>,  
                              property<edge_weight_t, double> > GraphvizDigraph;  
  
we still get errors such as this:  
  
```  
../../../boost/graph/detail/adjacency_list.hpp: In instantiation of ‘struct boost::vec_adj_list_any_vertex_pa::bind_<boost::vertex_attribute_t, boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_name_t, std::__cxx11::basic_string<char> >, boost::property<boost::edge_weight_t, double> >, boost::property<boost::vertex_name_t, std::__cxx11::basic_string<char> > >’:  
../../../boost/graph/detail/adjacency_list.hpp:2636:12:   required from ‘struct boost::detail::vec_adj_list_choose_vertex_pa<boost::vertex_attribute_t, boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_name_t, std::__cxx11::basic_string<char> >, boost::property<boost::edge_weight_t, double> >, boost::property<boost::vertex_name_t, std::__cxx11::basic_string<char> > >’  
../../../boost/graph/detail/adjacency_list.hpp:2762:12:   required from ‘struct boost::vec_adj_list_vertex_property_selector::bind_<boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_name_t, std::__cxx11::basic_string<char> >, boost::property<boost::edge_weight_t, double> >, boost::property<boost::vertex_name_t, std::__cxx11::basic_string<char> >, boost::vertex_attribute_t>’  
../../../boost/graph/properties.hpp:201:12:   required from ‘struct boost::detail::vertex_property_map<boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_name_t, std::__cxx11::basic_string<char> >, boost::property<boost::edge_weight_t, double> >, boost::vertex_attribute_t>’  
../../../boost/graph/properties.hpp:212:10:   required from ‘struct boost::property_map<boost::adjacency_list<boost::vecS, boost::vecS, boost::directedS, boost::property<boost::vertex_name_t, std::__cxx11::basic_string<char> >, boost::property<boost::edge_weight_t, double> >, boost::vertex_attribute_t>’  
scc.cpp:33:55:   required from here  
../../../boost/graph/detail/adjacency_list.hpp:2585:29: error: forming reference to void  
         typedef value_type& reference;  
                             ^  
../../../boost/graph/detail/adjacency_list.hpp:2586:35: error: forming reference to void  
         typedef const value_type& const_reference;  
                                   ^  
../../../boost/graph/detail/adjacency_list.hpp:2589:55: error: forming reference to void  
           <Graph, Graph*, value_type, reference, Tag> type;  
                                                       ^  
../../../boost/graph/detail/adjacency_list.hpp:2591:67: error: forming reference to void  
           <Graph, const Graph*, value_type, const_reference, Tag> const_type;  
                                                                   ^  
scc.cpp: In function ‘int main()’:  
scc.cpp:34:46: error: no matching function for call to ‘get(boost::vertex_attribute_t, GraphvizDigraph&)’  
     vertex_attr_map = get(vertex_attribute, g);  
  
```

---

## Comment 2

> Username: murraycu  
> Created_at: 2016-05-07 10:58:38 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-217628699  

So, shall we move the bgl book examples into examples/bgl-book/ ?

---

## Comment 3

> Username: maelvls  
> Created_at: 2016-05-09 14:17:29 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-217876997  

Hi,  
I am struggling with building `example/csr-example.cpp`.   
  
For `test/csr_graph_test.cpp` I found out (after hours of trying) that I had to build it using **`-std=c++11`** (**isn't it documented somewhere?**)  
  
```  
cd libs/graph/test  
../../../b2 toolset=darwin cxxflags="-std=c++11" ../../../bin.v2/libs/graph/test/csr_graph_test.test/darwin-4.2.1/debug/csr_graph_test  
```  
  
But I can't get `example/csr-example.cpp` to compile, even with your patch... 😕   
  
```  
cd libs/graph/example  
../../../b2 toolset=darwin cxxflags="-std=c++11" ../../../bin.v2/libs/graph/example/darwin-4.2.1/debug/csr-example.o  
```  
  
Does it build on yours? What command are you using? Thanks!

---

## Comment 4

> Username: murraycu  
> Created_at: 2016-05-09 19:00:56 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-217957177  

What makes you think that you need -std=C++11 ?  
  
csr-example does indeed have a compilation error where it uses write_graphviz(). All the examples that use graphviz API have build problems. I have not disabled them, at least yet, because I hope that someone with experience of that API, will come along and fix them if we make it obvious that the code is broken.  
  
I assume that you mean this compilation error:  
  
```  
In file included from csr-example.cpp:14:0:  
../../../boost/graph/graphviz.hpp: In instantiation of ‘void boost::write_graphviz(std::ostream&, const Graph&, VertexPropertiesWriter, EdgePropertiesWriter, GraphPropertiesWriter, VertexID, typename boost::enable_if_c<boost::is_base_and_derived<boost::vertex_list_graph_tag, typename boost::graph_traits<Graph>::traversal_category>::value, boost::graph::detail::no_parameter>::type) [with Graph = boost::compressed_sparse_row_graph<boost::directedS, WebPage>; VertexPropertiesWriter = boost::dynamic_properties; EdgePropertiesWriter = std::__cxx11::basic_string<char>; GraphPropertiesWriter = boost::typed_identity_property_map<long unsigned int>; VertexID = boost::typed_identity_property_map<long unsigned int>; std::ostream = std::basic_ostream<char>; typename boost::enable_if_c<boost::is_base_and_derived<boost::vertex_list_graph_tag, typename boost::graph_traits<Graph>::traversal_category>::value, boost::graph::detail::no_parameter>::type = boost::graph::detail::no_parameter]’:  
../../../boost/graph/graphviz.hpp:290:19:   required from ‘void boost::write_graphviz(std::ostream&, const Graph&, VertexPropertiesWriter, EdgePropertiesWriter, GraphPropertiesWriter, typename boost::enable_if_c<boost::is_base_and_derived<boost::vertex_list_graph_tag, typename boost::graph_traits<Graph>::traversal_category>::value, boost::graph::detail::no_parameter>::type) [with Graph = boost::compressed_sparse_row_graph<boost::directedS, WebPage>; VertexPropertiesWriter = boost::dynamic_properties; EdgePropertiesWriter = std::__cxx11::basic_string<char>; GraphPropertiesWriter = boost::typed_identity_property_map<long unsigned int>; std::ostream = std::basic_ostream<char>; typename boost::enable_if_c<boost::is_base_and_derived<boost::vertex_list_graph_tag, typename boost::graph_traits<Graph>::traversal_category>::value, boost::graph::detail::no_parameter>::type = boost::graph::detail::no_parameter]’  
csr-example.cpp:58:65:   required from here  
../../../boost/graph/graphviz.hpp:264:8: error: no match for call to ‘(boost::typed_identity_property_map<long unsigned int>) (std::ostream&)’  
     gpw(out); //print graph properties  
        ^  
../../../boost/graph/graphviz.hpp:270:10: error: no match for call to ‘(boost::dynamic_properties) (std::ostream&, const long unsigned int&)’  
       vpw(out, *i); //print vertex attributes  
          ^  
In file included from csr-example.cpp:14:0:  
../../../boost/graph/graphviz.hpp:276:10: error: no match for call to ‘(std::__cxx11::basic_string<char>) (std::ostream&, boost::iterators::detail::iterator_facade_base<boost::detail::csr_edge_iterator<boost::compressed_sparse_row_graph<boost::directedS, WebPage> >, boost::detail::csr_edge_descriptor<long unsigned int, long unsigned int>, boost::iterators::forward_traversal_tag, boost::detail::csr_edge_descriptor<long unsigned int, long unsigned int>, long int, false, false>::reference)’  
       epw(out, *ei); //print edge attributes  
          ^  
  
```  
  
I am just running bjam in the modular-boost/libs/graph/examples/ directory, either running just bjam to build all examples, or for instance, "bjam csr-example". I have no idea if that is wise.

---

## Comment 5

> Username: maelvls  
> Created_at: 2016-05-10 10:55:31 UTC  
> Updated_at: 2016-05-10 10:57:24 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-218124457  

For csr_graph_test, I use clang-703.0.29 (Apple LLVM version). The (shorten) command used in b2:  
  
```  
clang++ -I"../../.." -c "csr_graph_test.cpp"  
```  
  
When I looked for any regression issues, I found [those regression matrices](http://beta.boost.org/development/tests/develop/developer/graph.html). The `csr_graph_test` doesn't compile for most configurations, so I checked the difference between the working and non-working builds. Turns out the option `-std=c++11` or `-std=c++14` must be given, otherwise it won't compile.  
  
---  
  
For csr-example, I have the exact same error you shown. I use the command `../../../b2 csr-example`.

---

## Comment 6

> Username: murraycu  
> Created_at: 2016-05-10 11:48:25 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-218134514  

Maybe that link should be in our README. Unfortunately I don't see any information about when that build started failing.  
  
There doesn't seem to have been any change in csr_graph_test.cpp to make it need C++11 over the last few years and I doubt that there's a change in Boost Graph to make it need C++11. Maybe something used by Boost Graph now needs C++11, such as boost fusion or boost iterator.  
  
I very much wish that Boost Graph could start requiring C++11 or 14 anyway. As a first step to that, I have a branch that converts these examples to C++11:  
https://github.com/murraycu/graph/commits/develop-murrayc-examples-modern-cpp  
  
It contains these commits too, so I plan to submit it as a pull request when/if this one is accepted

---

## Comment 7

> Username: murraycu  
> Created_at: 2016-09-30 07:13:23 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-250675928  

Could this please be accepted or rejected, please? That would let me move forward with other stuff more easily.

---

## Comment 8

> Username: murraycu  
> Created_at: 2016-11-05 14:35:38 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-258615510  

Thanks. Is there any reason that you squashed these into one commit? The commits seemed mostly properly self-contained, making it easier to understand why a change was made when investigating later.

---

## Comment 9

> Username: Belcourt  
> Created_at: 2016-11-05 18:22:11 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-258631305  

I generally avoid merge commits, and Github by default always suggests squashing multiple commits (the Squash and Merge button), so I generally accept their default behavior.

---

## Comment 10

> Username: murraycu  
> Created_at: 2016-11-05 22:43:45 UTC  
> Url: https://github.com/boostorg/graph/pull/64#issuecomment-258647284  

Yes, I dislike merge commits too. I generally just pull from branches, using the command line if necessary, after rebasing the branch. Please try to avoid squashing commits unless they really belong in one commit - it leaves a real mess. Feel free to ask me to rebase and force push my branches if you ever need it.

---
