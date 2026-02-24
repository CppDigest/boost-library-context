# #60 refactor: avoid using deprecated Boost headers [Merged]

> Username: fanquake  
> Created at: 2022-04-03 16:07:12 UTC  
> Updated at: 2022-04-10 17:20:00 UTC  
> Merged at: 2022-04-10 17:00:33 UTC  
> Closed at: 2022-04-10 17:00:33 UTC  
> Url: https://github.com/boostorg/signals2/pull/60  

`boost/ref.hpp` and `boost/swap.hpp` are both deprecated:  
```cpp  
#ifndef BOOST_REF_HPP  
#define BOOST_REF_HPP  
  
// The header file at this path is deprecated;  
// use boost/core/ref.hpp instead.  
  
#include <boost/core/ref.hpp>  
  
#endif  
```  
  
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

> Username: fmhess  
> Created_at: 2022-04-03 23:02:11 UTC  
> Url: https://github.com/boostorg/signals2/pull/60#issuecomment-1086967354  

Seems fine, I won't be able to merge it until next week though.

---
