# #258 - BOOST_GRAPH_MPI_INCLUDE formatting [Open]

> Username: cojuer  
> Created at: 2021-05-13 12:26:48 UTC  
> Updated at: 2021-05-13 12:26:48 UTC  
> Url: https://github.com/boostorg/graph/issues/258  

When trying to build parallel graph examples using boost 1.76 I get errors like this:  
```  
In file included from /usr/local/include/boost/graph/breadth_first_search.hpp:26,  
                 from src.cpp:19:  
/usr/local/include/boost/graph/two_bit_color_map.hpp:106:34: fatal error:  boost / graph / distributed / two_bit_color_map.hpp: No such file or directory  
  106 | #include BOOST_GRAPH_MPI_INCLUDE(< boost / graph / distributed / two_bit_color_map.hpp >)  
      |  
```  
Seems to be caused  by new formatting introduced after 1.72 when:   
`BOOST_GRAPH_MPI_INCLUDE(<boost/graph/distributed/two_bit_color_map.hpp>)`   
was replaced with   
`BOOST_GRAPH_MPI_INCLUDE(< boost / graph / distributed / two_bit_color_map.hpp >)`
