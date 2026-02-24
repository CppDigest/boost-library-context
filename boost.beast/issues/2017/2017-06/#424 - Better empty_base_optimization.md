# #424 - Better empty_base_optimization [Closed]

> Username: vinniefalco  
> Created at: 2017-06-05 15:55:55 UTC  
> Updated at: 2017-08-04 23:09:29 UTC  
> Closed at: 2017-08-04 23:09:29 UTC  
> Url: https://github.com/boostorg/beast/issues/424  

This could help  
https://github.com/boostorg/type_traits/pull/4/commits/5d936acc05e79f60bae84e4c61d46aee759c93c7  
  
Also  
http://www.boost.org/doc/libs/1_64_0/libs/utility/doc/html/compressed_pair.html  
  
And `std::is_final` when available (use boost/config.hpp to find out)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 05:37:24 UTC  
> Url: https://github.com/boostorg/beast/issues/424#issuecomment-307003668  

Actually we should avoid using this if possible. `boost:;compressed_pair` as a data member could be a better approach.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-04 23:09:29 UTC  
> Url: https://github.com/boostorg/beast/issues/424#issuecomment-320372021  

Done
