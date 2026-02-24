# #37 address_of.hpp: Fix namespace ambiguity [Closed]

> Username: jzmaddock  
> Created at: 2017-12-25 17:21:07 UTC  
> Updated at: 2018-02-11 06:53:55 UTC  
> Closed at: 2017-12-26 01:35:29 UTC  
> Url: https://github.com/boostorg/core/pull/37  

I'm seeing compiler errors with vc12, when testing serialization of Boost.Multiprecision types - there is ambiguity between boost::detail and boost::multiprecision::detail.  I assume the error occurs for serialization of any type that has it's own detail namespace.  The fix is trivially to qualify the use of "detail::".

---

## Comment 1

> Username: glenfe  
> Created_at: 2017-12-26 01:35:29 UTC  
> Url: https://github.com/boostorg/core/pull/37#issuecomment-353901784  

Merged.

---
