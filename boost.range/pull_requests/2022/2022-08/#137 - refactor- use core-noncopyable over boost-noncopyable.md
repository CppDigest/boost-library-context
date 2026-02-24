# #137 refactor: use core/noncopyable over boost/noncopyable [Merged]

> Username: fanquake  
> Created at: 2022-08-23 15:00:42 UTC  
> Updated at: 2022-08-28 14:30:33 UTC  
> Merged at: 2022-08-24 16:55:02 UTC  
> Closed at: 2022-08-24 16:55:02 UTC  
> Url: https://github.com/boostorg/range/pull/137  

The later is deprecated:  
```cpp  
// The header file at this path is deprecated;  
// use boost/core/noncopyable.hpp instead.  
  
#include <boost/core/noncopyable.hpp>  
```

---
