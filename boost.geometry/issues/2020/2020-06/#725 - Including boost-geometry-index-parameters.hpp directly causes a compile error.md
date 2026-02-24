# #725 - Including boost/geometry/index/parameters.hpp directly causes a compile error [Closed]

> Username: rconde01  
> Created at: 2020-06-11 18:52:07 UTC  
> Updated at: 2020-06-15 15:45:17 UTC  
> Closed at: 2020-06-15 15:44:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/725  

#include <boost/geometry/index/parameters.hpp>  
  
https://godbolt.org/z/ebxrBQ  
  
`In file included from <source>:1:  
  
/celibs/boost_1_73_0/boost/geometry/index/parameters.hpp:292:13: error: unknown type name 'default_strategy'  
    typedef default_strategy type;  
/celibs/boost_1_73_0/boost/geometry/index/parameters.hpp:293:13: error: unknown type name 'default_strategy'  
    typedef default_strategy result_type;  
/celibs/boost_1_73_0/boost/geometry/index/parameters.hpp:307:19: error: unknown type name 'default_strategy'  
    static inline default_strategy apply(Parameters const&)  
/celibs/boost_1_73_0/boost/geometry/index/parameters.hpp:309:16: error: use of undeclared identifier 'default_strategy'  
        return default_strategy();`

---

## Comment 1

> Username: vissarion  
> Created at: 2020-06-15 12:35:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/725#issuecomment-644107843  

It is not clear why you want to include only parameters, but a workaround could be to add  
```#include <boost/geometry/strategies/default_strategy.hpp>```  
before your include.

---

## Comment 2

> Username: vissarion  
> Created at: 2020-06-15 12:39:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/725#issuecomment-644110025  

Moreover, this is a general issue of the library, see https://github.com/boostorg/geometry/issues/523

---

## Comment 3

> Username: rconde01  
> Created at: 2020-06-15 15:44:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/725#issuecomment-644213579  

Yes that was my workaround. I just mentioned it because I encountered it when updating boost versions.

---

## Comment 4

> Username: rconde01  
> Created at: 2020-06-15 15:45:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/725#issuecomment-644214273  

Close as it's a dupe of #523
