# #53 refactor: use core/noncopyable over boost/noncopyable [Merged]

> Username: fanquake  
> Created at: 2022-08-24 11:35:22 UTC  
> Updated at: 2022-09-01 08:47:34 UTC  
> Merged at: 2022-09-01 08:45:32 UTC  
> Closed at: 2022-09-01 08:45:32 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/53  

The later is deprecated:  
```cpp  
// The header file at this path is deprecated;  
// use boost/core/noncopyable.hpp instead.  
  
#include <boost/core/noncopyable.hpp>  
```

---

## Comment 1

> Username: apolukhin  
> Created_at: 2022-09-01 08:45:41 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/53#issuecomment-1233954815  

Many thanks for the PR!

---
