# #57 refactor: use boost/core/noncopyable.hpp over boost/noncopyable.hpp [Merged]

> Username: fanquake  
> Created at: 2022-04-01 21:07:09 UTC  
> Updated at: 2022-04-03 15:51:14 UTC  
> Merged at: 2022-04-03 13:27:18 UTC  
> Closed at: 2022-04-03 13:27:18 UTC  
> Url: https://github.com/boostorg/multi_index/pull/57  

`boost/noncopyable.hpp` is deprecated:  
```cpp  
#ifndef BOOST_NONCOPYABLE_HPP  
#define BOOST_NONCOPYABLE_HPP  
  
// The header file at this path is deprecated;  
// use boost/core/noncopyable.hpp instead.  
  
#include <boost/core/noncopyable.hpp>  
  
#endif  
```

---

## Comment 1

> Username: joaquintides  
> Created_at: 2022-04-03 13:27:58 UTC  
> Url: https://github.com/boostorg/multi_index/pull/57#issuecomment-1086869188  

Merged, thank you! Will try to get it shipped in Boost 1.79.

---

## Comment 2

> Username: joaquintides  
> Created_at: 2022-04-03 15:07:22 UTC  
> Url: https://github.com/boostorg/multi_index/pull/57#issuecomment-1086887885  

Included in Boost 1.79, due Apr 13.

---

## Comment 3

> Username: fanquake  
> Created_at: 2022-04-03 15:51:12 UTC  
> Url: https://github.com/boostorg/multi_index/pull/57#issuecomment-1086895881  

> Included in Boost 1.79, due Apr 13.  
  
Thanks.

---
