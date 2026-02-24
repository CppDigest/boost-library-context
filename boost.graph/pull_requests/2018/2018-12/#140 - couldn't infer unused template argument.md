# #140 couldn't infer unused template argument [Merged]

> Username: aleden  
> Created at: 2018-12-11 20:16:01 UTC  
> Updated at: 2019-01-26 09:57:26 UTC  
> Merged at: 2019-01-26 09:57:25 UTC  
> Closed at: 2019-01-26 09:57:25 UTC  
> Url: https://github.com/boostorg/graph/pull/140  

Couldn't compile code calling boost::copy_component because got compiler error from clang7:  
  
> /usr/include/boost/graph/copy.hpp:399:58: note: candidate template ignored: couldn't infer template argument 'Graph'  
>       typename graph_traits<NewGraph>::vertex_descriptor copy_one_vertex(Vertex u) const {  
>   
  
I noticed that the 'Graph' template argument is un-referenced within graph_copy_visitor::copy_one_vertex, so I removed it. Afterwards, example compiles and runs.

---
