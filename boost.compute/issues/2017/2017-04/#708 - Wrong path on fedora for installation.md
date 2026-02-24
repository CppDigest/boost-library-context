# #708 - Wrong path on fedora for installation [Open]

> Username: navidR  
> Created at: 2017-04-20 17:11:02 UTC  
> Updated at: 2017-10-11 22:15:24 UTC  
> Url: https://github.com/boostorg/compute/issues/708  

```  
...  
-- Installing: /usr/local/include/compute/boost/compute/type_traits/scalar_type.hpp  
-- Installing: /usr/local/include/compute/boost/compute/type_traits/type_definition.hpp  
-- Installing: /usr/local/include/compute/boost/compute/type_traits/type_name.hpp  
-- Installing: /usr/local/include/compute/boost/compute/type_traits/vector_size.hpp  
-- Installing: /usr/local/include/compute/boost/compute/types.hpp  
-- Installing: /usr/local/include/compute/boost/compute/types  
-- Installing: /usr/local/include/compute/boost/compute/types/builtin.hpp  
-- Installing: /usr/local/include/compute/boost/compute/types/complex.hpp  
...  
```  
  
But library itself uses   
  
```  
#include <boost/compute/buffer.hpp>  
```  
(for example)  
  
So the installation path should be   
```  
-- Installing: /usr/local/include/boost/compute/type_traits/vector_size.hpp  
-- Installing: /usr/local/include/boost/compute/types.hpp  
-- Installing: /usr/local/include/boost/compute/types  
```  
I made fork and fixed it for myself. I don't know should I make a PR (I just changed installation path in CMake install target and CMake configuration file).
