# #133 Remove the use of snprintf for trivial formatting in lightweight_test [Merged]

> Username: Lastique  
> Created at: 2022-12-09 01:18:54 UTC  
> Updated at: 2022-12-09 17:00:41 UTC  
> Merged at: 2022-12-09 16:55:14 UTC  
> Closed at: 2022-12-09 16:55:14 UTC  
> Url: https://github.com/boostorg/core/pull/133  

This implements in-place formatting for small hex numbers instead of using `snprintf` with the associated portability scaffolding. This also removes the need for `#include <cstdio>`.  
  
(I was going to use `boost/core/snprintf.hpp` initially, but figured it would be better to remove `snprintf` altogether.)

---
