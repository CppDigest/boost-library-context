# #302 Remove dispatching via mpl and stick to boost::integral_constant. [Merged]

> Username: jzmaddock  
> Created at: 2020-01-13 15:15:53 UTC  
> Updated at: 2020-01-15 20:15:09 UTC  
> Merged at: 2020-01-14 09:38:54 UTC  
> Closed at: 2020-01-14 09:38:55 UTC  
> Url: https://github.com/boostorg/math/pull/302  

Simplify the dispatching logic to use mpl::if_ and broken compiler workarounds less often.  
Fixes: https://github.com/boostorg/type_traits/issues/133.

---
