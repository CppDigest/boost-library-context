# #315 Fix deprecated usage of <boost/bind.hpp> [Merged]

> Username: TaWeiTu  
> Created at: 2020-08-07 06:40:53 UTC  
> Updated at: 2021-06-09 11:16:19 UTC  
> Merged at: 2021-06-09 11:16:19 UTC  
> Closed at: 2021-06-09 11:16:19 UTC  
> Url: https://github.com/boostorg/python/pull/315  

Using `<boost/bind.hpp>` without `BOOST_BIND_GLOBAL_PLACEHOLDERS` is deprecated.  
This patch replaces the usage of `<boost/bind.hpp>` with `<boost/bind/bind.hpp>` and inserts   
`using namespace boost::placeholders` when necessary.

---
