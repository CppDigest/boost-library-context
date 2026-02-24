# #28 Add make_void and void_t traits [Merged]

> Username: glenfe  
> Created at: 2017-01-30 23:11:26 UTC  
> Updated at: 2017-02-07 19:19:28 UTC  
> Merged at: 2017-02-07 18:50:21 UTC  
> Closed at: 2017-02-07 18:50:21 UTC  
> Url: https://github.com/boostorg/type_traits/pull/28  

C++17 added `std::void_t` and this provides `boost::void_t` and `boost::make_void`, which are commonly used in `decltype` SFINAE expressions.

---

## Comment 1

> Username: glenfe  
> Created_at: 2017-02-04 18:27:23 UTC  
> Url: https://github.com/boostorg/type_traits/pull/28#issuecomment-277464950  

Let me know if the documentation needs to convey that the trait supports C++03  with a single type parameter, and arbitrary number of parameters when BOOST_NO_CXX11_VARIADIC_TEMPLATES is not present.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-02-07 19:03:09 UTC  
> Url: https://github.com/boostorg/type_traits/pull/28#issuecomment-278106039  

See also a few small improvements in https://github.com/boostorg/type_traits/commit/7b29d67a3e1eb759bef7a303ed7750c463245877

---

## Comment 3

> Username: glenfe  
> Created_at: 2017-02-07 19:19:28 UTC  
> Url: https://github.com/boostorg/type_traits/pull/28#issuecomment-278110758  

Thanks John!

---
