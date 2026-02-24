# #32 - Make use of __has_include conditional [Closed]

> Username: DanielaE  
> Created at: 2019-12-26 07:59:22 UTC  
> Updated at: 2019-12-29 19:55:03 UTC  
> Closed at: 2019-12-29 19:54:21 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/32  

E.g. from `fwd.h` lines 11-14:  
  
```  
#if 201703L <= __cplusplus && __has_include(<stl2/ranges.hpp>) && \  
    !defined(BOOST_STL_INTERFACES_DISABLE_CMCSTL2)  
#include <stl2/ranges.hpp>  
#endif  
```  
  
If a compiler doesn't implement _has-include-expressions_ [`has_include()`](http://eel.is/c++draft/cpp#nt:has-include-expression) from C++ 17, then it will complain about unexpected _pp-tokens_ when processing line 11 in phase 4. Therefore change the code from the example like so:  
  
```  
#if 201703L <= __cplusplus && defined(__has_include)  
#if	__has_include(<stl2/ranges.hpp>) && \  
    !defined(BOOST_STL_INTERFACES_DISABLE_CMCSTL2)  
#include <stl2/ranges.hpp>  
#endif  
#endif  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2019-12-29 19:55:03 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/32#issuecomment-569536687  

Fixed.  Thanks, Daniela.
