# #296 Fix boost/bind deprecation build warnings [Closed]

> Username: tkieft  
> Created at: 2020-03-23 17:29:28 UTC  
> Updated at: 2021-06-02 18:04:38 UTC  
> Closed at: 2021-06-02 18:04:38 UTC  
> Url: https://github.com/boostorg/python/pull/296  

Including `boost/bind.hpp` is now deprecated in favor of `boost/bind/bind.hpp`, and you must use namespaced placeholders such as `boost::placeholders::_1`.

---
