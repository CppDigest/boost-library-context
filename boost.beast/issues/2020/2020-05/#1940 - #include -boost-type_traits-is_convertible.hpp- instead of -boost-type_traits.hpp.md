# #1940 - #include <boost/type_traits/is_convertible.hpp> instead of <boost/type_traits.hpp> [Closed]

> Username: Anton-V-K  
> Created at: 2020-05-04 15:44:31 UTC  
> Updated at: 2020-05-05 00:18:47 UTC  
> Closed at: 2020-05-05 00:18:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1940  

There is a fragment in `.../beast/core/buffers_prefix.hpp`:  
```  
#if BOOST_WORKAROUND(BOOST_MSVC, < 1910)  
#include <boost/type_traits.hpp>  
#endif  
```  
Actually only `boost::is_convertible` is used, so the include statement can reference only `boost/type_traits/is_convertible.hpp`:  
```  
#if BOOST_WORKAROUND(BOOST_MSVC, < 1910)  
#include <boost/type_traits/is_convertible.hpp>  
#endif  
```  
Such replacement can reduce dependency and is handy if only subset of boost files is available.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-04 16:20:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1940#issuecomment-623563058  

@Anton-V-K,   
Thanks very much for this. I'll get this into the next release right away.
