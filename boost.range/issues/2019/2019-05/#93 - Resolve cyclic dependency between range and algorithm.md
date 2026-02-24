# #93 - Resolve cyclic dependency between range and algorithm [Closed]

> Username: jeking3  
> Created at: 2019-05-05 11:29:42 UTC  
> Updated at: 2019-05-05 15:01:23 UTC  
> Closed at: 2019-05-05 15:01:22 UTC  
> Url: https://github.com/boostorg/range/issues/93  

algorithm depends on range quite a bit, however range also depends on algorithm in:  
  
```  
include/boost/range/algorithm/min_element.hpp:#include <boost/algorithm/minmax_element.hpp>  
include/boost/range/algorithm/max_element.hpp:#include <boost/algorithm/minmax_element.hpp>  
```  
  
This cyclic dependency should be resolved.  Perhaps minmax_element.hpp should be moved from algorithm to range?  Nothing else in algorithm includes it.  This would break the cyclic dependency between these modules.  
  
cc: @mclow

---

## Comment 1

> Username: jeking3  
> Created at: 2019-05-05 15:01:22 UTC  
> Url: https://github.com/boostorg/range/issues/93#issuecomment-489434419  

Looks like this was resolved by 4ec46acdf585d421770228950a271473345a54a0
