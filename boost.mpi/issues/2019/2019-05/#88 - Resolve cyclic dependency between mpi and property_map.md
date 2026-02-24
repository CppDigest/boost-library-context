# #88 - Resolve cyclic dependency between mpi and property_map [Closed]

> Username: jeking3  
> Created at: 2019-05-05 15:38:07 UTC  
> Updated at: 2019-06-11 17:33:50 UTC  
> Closed at: 2019-06-11 17:33:50 UTC  
> Url: https://github.com/boostorg/mpi/issues/88  

property_map depends on mpi:  
```  
property_map/include/boost/property_map/parallel/detail/untracked_pair.hpp:#include <boost/mpi/datatype.hpp>  
property_map/include/boost/property_map/parallel/unsafe_serialize.hpp:#include <boost/mpi/datatype.hpp>  
```  
  
mpi depends on property_map:  
```  
mpi/include/boost/mpi/graph_communicator.hpp:#include <boost/property_map/property_map.hpp>  
```  
  
Direct dependency cycles should be avoided to prepare boost for a future where each repository releases independently.

---

## Comment 1

> Username: aminiussi  
> Created at: 2019-06-11 17:33:50 UTC  
> Url: https://github.com/boostorg/mpi/issues/88#issuecomment-500946371  

fixed in #90
