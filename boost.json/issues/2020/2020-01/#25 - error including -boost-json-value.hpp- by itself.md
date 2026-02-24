# #25 - error including <boost/json/value.hpp> by itself [Closed]

> Username: vinniefalco  
> Created at: 2020-01-03 01:29:39 UTC  
> Updated at: 2020-05-02 01:06:03 UTC  
> Closed at: 2020-05-02 01:06:03 UTC  
> Url: https://github.com/boostorg/json/issues/25  

A file with just the one include:  
```  
#include <boost/json/value.hpp>  
```  
  
fails to compile. This is in the header-only configuration.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-02 01:06:03 UTC  
> Url: https://github.com/boostorg/json/issues/25#issuecomment-622646394  

Seems fixed
