# #490 - Better standalone error message? [Closed]

> Username: NAThompson  
> Created at: 2022-02-07 03:26:53 UTC  
> Updated at: 2022-09-01 17:19:03 UTC  
> Closed at: 2022-09-01 17:19:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/490  

When using the standalone, I get:  
  
```  
In file included from ./include/boost/multiprecision/mpfr.hpp:9:  
In file included from ./include/boost/multiprecision/number.hpp:10:  
./include/boost/multiprecision/detail/standalone_config.hpp:15:10: fatal error: 'boost/config.hpp' file not found  
#include <boost/config.hpp>  
         ^~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
This *is* an expected error:  
  
```  
// Boost.Config is dependency free so it is considered a requirement to use Boost.Multiprecision in standalone mode  
#include <boost/config.hpp>  
```  
  
but could it give a nice error message as to why it's not supported using `__has_include`?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-30 20:12:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/490#issuecomment-1232202883  

I think this needs to be moved to [Boost.Multiprecision](https://github.com/boostorg/multiprecision).
