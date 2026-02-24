# #300 - GCC pedantic warning in include/boost/python/detail/caller.hpp [Open]

> Username: EugeneZelenko  
> Created at: 2020-04-04 16:43:29 UTC  
> Updated at: 2020-04-04 16:43:29 UTC  
> Url: https://github.com/boostorg/python/issues/300  

GCC 7.5 fires pedantic warning in Boost 1.72:  
  
include/boost/python/detail/caller.hpp:127:2: error: extra ‘;’ [-Werror=pedantic]  
  
Code in question is function template get_ret().
