# #90 refactor: use core/swap over deprecated swap header [Merged]

> Username: fanquake  
> Created at: 2022-04-29 15:09:38 UTC  
> Updated at: 2022-04-29 15:27:18 UTC  
> Merged at: 2022-04-29 15:26:16 UTC  
> Closed at: 2022-04-29 15:26:16 UTC  
> Url: https://github.com/boostorg/utility/pull/90  

In boost/swap.hpp:  
```cpp  
#ifndef BOOST_SWAP_HPP  
#define BOOST_SWAP_HPP  
  
// The header file at this path is deprecated;  
// use boost/core/swap.hpp instead.  
  
#include <boost/core/swap.hpp>  
  
#endif  
  
```

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-04-29 15:27:18 UTC  
> Url: https://github.com/boostorg/utility/pull/90#issuecomment-1113448065  

Thanks.

---
