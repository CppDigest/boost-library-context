# #16 Fix compilation against Boost Thread v4 [Merged]

> Username: Orphis  
> Created at: 2017-06-20 13:00:22 UTC  
> Updated at: 2017-07-14 03:03:32 UTC  
> Merged at: 2017-07-13 13:40:15 UTC  
> Closed at: 2017-07-13 13:40:15 UTC  
> Url: https://github.com/boostorg/locale/pull/16  

The modified files use boost::unique_lock and don't include the  
proper header. In Boost Thread <4, it would be transitively included  
from <boost/thread/mutex.hpp> but no longer with v4 by default.

---

## Comment 1

> Username: Orphis  
> Created_at: 2017-07-14 03:03:32 UTC  
> Url: https://github.com/boostorg/locale/pull/16#issuecomment-315257490  

Thanks!

---
