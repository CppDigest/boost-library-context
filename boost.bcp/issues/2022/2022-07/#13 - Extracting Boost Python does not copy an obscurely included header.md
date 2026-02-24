# #13 - Extracting Boost Python does not copy an obscurely included header [Closed]

> Username: foundry-markf  
> Created at: 2022-07-05 16:05:04 UTC  
> Updated at: 2022-07-14 11:13:12 UTC  
> Closed at: 2022-07-14 11:13:12 UTC  
> Url: https://github.com/boostorg/bcp/issues/13  

Hi,  
  
I'm not sure if this is a bug or not, as it's quite obscure.  
  
Example using Boost 1.79.0: https://gist.github.com/foundry-markf/89e0d9893ecf6614821e4d55188be158  
  
In short, extracting and building Boost Python using bcp, results in an error failing to find a header:  
```  
In file included from libs/python/src/object/inheritance.cpp:7:  
In file included from ./boost/graph/breadth_first_search.hpp:26:  
./boost/graph/two_bit_color_map.hpp:106:10: fatal error: 'boost/graph/detail/empty_header.hpp' file not found  
#include BOOST_GRAPH_MPI_INCLUDE(<boost/graph/distributed/two_bit_color_map.hpp>)  
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/graph/detail/mpi_include.hpp:13:36: note: expanded from macro 'BOOST_GRAPH_MPI_INCLUDE'  
#define BOOST_GRAPH_MPI_INCLUDE(x) <boost/graph/detail/empty_header.hpp>  
                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
This header is rather obscurely included, from its definition  
  
```  
#if defined BOOST_GRAPH_USE_MPI  
#define BOOST_GRAPH_MPI_INCLUDE(x) x  
#else  
#define BOOST_GRAPH_MPI_INCLUDE(x) <boost/graph/detail/empty_header.hpp>  
#endif  
```  
  
and use  
  
```  
#include BOOST_GRAPH_MPI_INCLUDE(< boost / graph / distributed / two_bit_color_map.hpp >)  
```  
  
so I'm not surprised at the error.  
  
The question here is _should_ bcp cope with this?  
  
I've included in the gist, a workaround, by specifying the missing header on the module list, so this is not a blocker for me.  
  
Thanks.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-07-11 17:47:30 UTC  
> Url: https://github.com/boostorg/bcp/issues/13#issuecomment-1180693158  

We have the ability to fix this, so we might as well ;)  
  
See referenced PR above.
