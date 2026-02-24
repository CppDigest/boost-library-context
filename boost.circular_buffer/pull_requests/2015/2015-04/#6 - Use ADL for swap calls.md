# #6 Use ADL for swap calls. [Closed]

> Username: amerry  
> Created at: 2015-04-20 15:16:00 UTC  
> Updated at: 2015-04-21 08:01:53 UTC  
> Closed at: 2015-04-21 08:01:53 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/6  

https://svn.boost.org/trac/boost/ticket/11208

---

## Comment 1

> Username: apolukhin  
> Created_at: 2015-04-20 19:04:06 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/6#issuecomment-94542329  

Thanks for the patch, but it's better to use `boost/move/adl_move_swap.hpp` here. Otherwise we'll get the error close to the [this one](https://svn.boost.org/trac/boost/ticket/10882).  
  
Please, next time, make pull request against `develop` branch.

---
