# #186 [algorithms] Replace A and B BOOST_VARIANT_ENUM_PARAMS parameters with T... [Merged]

> Username: awulkiew  
> Created at: 2014-12-15 22:47:45 UTC  
> Updated at: 2014-12-21 11:06:02 UTC  
> Merged at: 2014-12-16 22:15:35 UTC  
> Closed at: 2014-12-16 22:15:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/186  

This PR is an attempt to fix two tickets:  
https://svn.boost.org/trac/boost/ticket/10467  
https://svn.boost.org/trac/boost/ticket/10863  
  
It replaces `A` and `B` parameters of `BOOST_VARIANT_ENUM_PARAMS(xxx)` with `T1` and `T2` respectively, which should play well with `B0` macro definition from `termios.h`. Replacement names was choosen because such names are already used in the library.  
  
An alternative would be to use `T` and `U`, which seems to be the most commonly used template parameters names around Boost, e.g. `U0` is used in Fusion, Iostreams, TypeErasure. Shorter names means shorter symbols which is good for the compiler.

---

## Comment 1

> Username: brunolalande  
> Created_at: 2014-12-20 22:10:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/186#issuecomment-67751739  

Thanks for fixing this Adam, I'm not sure why I followed a different naming for those specific algos...

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-12-21 11:06:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/186#issuecomment-67766845  

It was added later, by a different person :)

---
