# #182 - Python should default to static linkage [Open]

> Username: jolting  
> Created at: 2017-12-21 21:47:10 UTC  
> Updated at: 2017-12-21 21:53:08 UTC  
> Url: https://github.com/boostorg/python/issues/182  

https://github.com/boostorg/python/blob/d6d54ce483c27ef2cddb4d43e6e775c5c496ec62/include/boost/python/detail/config.hpp#L62  
  
I think this would make boost-python more consistent with the rest of boost:  
```cpp  
- # elif !defined(BOOST_PYTHON_DYNAMIC_LIB)  
+ # elif defined(BOOST_ALL_DYN_LINK) || defined(BOOST_PYTHON_DYN_LINK)  
```  
  
There was a thread about this back in 2008, but I didn't see any comments.  
https://lists.boost.org/Archives/boost/2008/12/146488.php

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-12-21 21:53:08 UTC  
> Url: https://github.com/boostorg/python/issues/182#issuecomment-353466942  

I'm all for simplifying or harmonising code, especially if the user-facing changes are well documented, and care is taken for backward compatibility.
