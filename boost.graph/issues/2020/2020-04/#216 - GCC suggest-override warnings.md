# #216 - GCC suggest-override warnings [Open]

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:33:06 UTC  
> Updated at: 2022-12-10 06:12:13 UTC  
> Url: https://github.com/boostorg/graph/issues/216  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/graph/graphml.hpp:41:25: warning: ‘virtual const char* boost::parse_error::what() const’ can be marked override [-Wsuggest-override]  
./boost/graph/graphviz.hpp:622:23: warning: ‘virtual const char* boost::graph_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/graph/graphviz.hpp:633:15: warning: ‘virtual const char* boost::bad_parallel_edge::what() const’ can be marked override [-Wsuggest-override]  
./boost/graph/graphviz.hpp:645:23: warning: ‘virtual const char* boost::directed_graph_error::what() const’ can be marked override [-Wsuggest-override]  
./boost/graph/graphviz.hpp:654:23: warning: ‘virtual const char* boost::undirected_graph_error::what() const’ can be marked override [-Wsuggest-override]  
./boost/graph/graphviz.hpp:665:15: warning: ‘virtual const char* boost::bad_graphviz_syntax::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-09 22:10:39 UTC  
> Url: https://github.com/boostorg/graph/issues/216#issuecomment-626241918  

See fixes in #217.

---

## Comment 2

> Username: hdu-sdlzx  
> Created at: 2022-12-10 06:12:13 UTC  
> Url: https://github.com/boostorg/graph/issues/216#issuecomment-1345151791  

Why not close this issue? @EugeneZelenko @jzmaddock
