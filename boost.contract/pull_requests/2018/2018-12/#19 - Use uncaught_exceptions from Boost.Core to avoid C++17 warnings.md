# #19 Use uncaught_exceptions from Boost.Core to avoid C++17 warnings [Closed]

> Username: Lastique  
> Created at: 2018-12-17 16:05:44 UTC  
> Updated at: 2019-01-06 17:01:42 UTC  
> Closed at: 2019-01-06 15:52:59 UTC  
> Url: https://github.com/boostorg/contract/pull/19  

In C++17, `std::uncaught_exception` is deprecated in favor of `std::uncaught_exceptions`. Use portable `uncaught_exceptions` implementation from Boost.Core, which is also available for C++03.  
  
This fixes gcc 8 warnings about using deprecated features in C++17 mode.

---

## Comment 1

> Username: lcaminiti  
> Created_at: 2018-12-21 11:06:24 UTC  
> Url: https://github.com/boostorg/contract/pull/19#issuecomment-449359723  

Yes, I will make this change when I have some time in the next few months (this fixed issue #16).

---

## Comment 2

> Username: lcaminiti  
> Created_at: 2019-01-06 15:52:59 UTC  
> Url: https://github.com/boostorg/contract/pull/19#issuecomment-451752098  

I implemented a fix for this in develop branch (will merged in master for next Boost release). I ended up not using boost::core::uncaught_exception just because I don't need to emulate the exception count. Instead, I use a #ifdef to call std::uncaught_exceptions (plural) when available on later compilers, and kept std::uncaught_exception (singular) on older compilers.

---
