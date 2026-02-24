# #46 - Usage of boost/utility/addressof.hpp ? [Closed]

> Username: njlr  
> Created at: 2018-11-14 01:43:43 UTC  
> Updated at: 2018-11-14 09:15:16 UTC  
> Closed at: 2018-11-14 09:15:16 UTC  
> Url: https://github.com/boostorg/iterator/issues/46  

I think this is now incorrect?   
  
https://github.com/boostorg/iterator/blob/develop/include/boost/iterator/iterator_facade.hpp#L19:  
  
```  
#include <boost/utility/addressof.hpp>  
```  
  
should be   
  
```  
#include <boost/core/addressof.hpp>  
```  
  
https://github.com/boostorg/align/pull/3#issuecomment-48072039

---

## Comment 1

> Username: eldiener  
> Created at: 2018-11-14 01:59:06 UTC  
> Url: https://github.com/boostorg/iterator/issues/46#issuecomment-438509234  

Both currently work, the former for backward compatibility.

---

## Comment 2

> Username: njlr  
> Created at: 2018-11-14 09:15:16 UTC  
> Url: https://github.com/boostorg/iterator/issues/46#issuecomment-438591572  

I see. This header is in the `utility` folder, but is actaully part of boostorg/core.   
  
Thanks!
