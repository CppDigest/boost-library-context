# #192 - Unnecessary includes [Closed]

> Username: vinniefalco  
> Created at: 2016-11-17 22:34:11 UTC  
> Updated at: 2017-01-04 19:08:27 UTC  
> Closed at: 2017-01-04 19:08:27 UTC  
> Url: https://github.com/boostorg/beast/issues/192  

These dont belong in utf8_checker.hpp:  
```  
#include <beast/unit_test/dstream.hpp>  
#include <iostream>  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-04 19:08:27 UTC  
> Url: https://github.com/boostorg/beast/issues/192#issuecomment-270457924  

Fixed in b20 I believe
