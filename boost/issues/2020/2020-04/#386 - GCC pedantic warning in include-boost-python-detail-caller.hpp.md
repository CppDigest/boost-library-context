# #386 - GCC pedantic warning in include/boost/python/detail/caller.hpp [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-04 16:32:55 UTC  
> Updated at: 2020-04-04 16:43:16 UTC  
> Closed at: 2020-04-04 16:43:16 UTC  
> Url: https://github.com/boostorg/boost/issues/386  

GCC 7.5 fires pedantic warning in Boost 1.72:  
  
<pre>  
include/boost/python/detail/caller.hpp:127:2: error: extra ‘;’ [-Werror=pedantic]  
</pre>  
  
Code in question is function template `get_ret()`.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-04-04 16:43:16 UTC  
> Url: https://github.com/boostorg/boost/issues/386#issuecomment-609055964  

Sorry, wrong place.
