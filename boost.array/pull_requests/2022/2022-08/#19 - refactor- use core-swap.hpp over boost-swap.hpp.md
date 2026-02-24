# #19 refactor: use core/swap.hpp over boost/swap.hpp [Merged]

> Username: fanquake  
> Created at: 2022-08-23 14:56:26 UTC  
> Updated at: 2022-08-24 08:44:05 UTC  
> Merged at: 2022-08-23 16:23:29 UTC  
> Closed at: 2022-08-23 16:23:29 UTC  
> Url: https://github.com/boostorg/array/pull/19  

The later is deprecated:  
```cpp  
#ifndef BOOST_SWAP_HPP  
#define BOOST_SWAP_HPP  
  
// The header file at this path is deprecated;  
// use boost/core/swap.hpp instead.  
  
#include <boost/core/swap.hpp>  
  
#endif  
  
```

---
