# #90 refactor: use boost/core/checked_delete.hpp over boost/checked_delete.hpp [Merged]

> Username: fanquake  
> Created at: 2022-04-03 15:50:51 UTC  
> Updated at: 2022-05-20 00:45:09 UTC  
> Merged at: 2022-05-20 00:45:09 UTC  
> Closed at: 2022-05-20 00:45:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/90  

boost/checked_delete.hpp has been deprecated:  
```cpp  
#ifndef BOOST_CHECKED_DELETE_HPP  
#define BOOST_CHECKED_DELETE_HPP  
  
// The header file at this path is deprecated;  
// use boost/core/checked_delete.hpp instead.  
  
#include <boost/core/checked_delete.hpp>  
  
#endif  
```

---
