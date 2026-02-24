# #101 Fix build when `BOOST_BIND_NO_PLACEHOLDERS` is defined [Merged]

> Username: Osyotr  
> Created at: 2022-07-14 21:27:50 UTC  
> Updated at: 2023-11-16 15:29:21 UTC  
> Merged at: 2023-11-16 15:11:40 UTC  
> Closed at: 2023-11-16 15:11:40 UTC  
> Url: https://github.com/boostorg/property_tree/pull/101  

`<boost/bind/placeholders.hpp>` should be explicitly included here, because `<boost/bind/bind.hpp>` may not include it depending on whether `BOOST_BIND_NO_PLACEHOLDERS` is defined or not.

---
