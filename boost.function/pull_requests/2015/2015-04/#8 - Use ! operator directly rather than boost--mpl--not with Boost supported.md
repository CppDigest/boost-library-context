# #8 Use ! operator directly rather than boost::mpl::not with Boost supported... [Merged]

> Username: eldiener  
> Created at: 2015-04-27 08:24:59 UTC  
> Updated at: 2015-04-27 20:00:41 UTC  
> Merged at: 2015-04-27 20:00:41 UTC  
> Closed at: 2015-04-27 20:00:41 UTC  
> Url: https://github.com/boostorg/function/pull/8  

Changed at suggestion of John Maddock to use C++ operators directly for current Boost supported compilers rather than use equivalent boost::mpl constructs, when eliminating dependency on deprecated type_traits ice_xxx.hpp headers.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-04-27 14:04:06 UTC  
> Updated_at: 2015-04-27 14:04:23 UTC  
> Url: https://github.com/boostorg/function/pull/8#issuecomment-96666472  

I think you should eliminate the workaround for **BORLANDC** as well. We don't have any testers for that compiler anymore, and a quick search of the ML over the last few months suggests no one is using it.  (Yes, I know that this is feature-creep)

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-04-27 18:47:17 UTC  
> Url: https://github.com/boostorg/function/pull/8#issuecomment-96777271  

I eliminated the Borland workaround.

---
