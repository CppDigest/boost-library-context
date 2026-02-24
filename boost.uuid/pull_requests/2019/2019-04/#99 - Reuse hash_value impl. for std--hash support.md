# #99 Reuse hash_value impl. for std::hash support [Closed]

> Username: brad-anderson  
> Created at: 2019-04-15 21:44:18 UTC  
> Updated at: 2019-04-15 22:14:06 UTC  
> Closed at: 2019-04-15 22:14:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/99  

A temporary string allocation shouldn't be necessary for hashing  
  
Replaces the messy PR #98

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-04-15 21:56:02 UTC  
> Url: https://github.com/boostorg/uuid/pull/99#issuecomment-483433480  

I think, it'd be better implemented as `hash_value(uuid const&)` overload (which would be implemented in terms of `hash_range` and without `to_string`). `std::hash` specialization then can forward to `hash_value`. The benefit of this approach is that you get integration into Boost.ContainerHash infrastructure and thus automatic support for any containers relying on Boost.ContainerHash.

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-04-15 21:58:35 UTC  
> Url: https://github.com/boostorg/uuid/pull/99#issuecomment-483434215  

Also, the implementation should check for `BOOST_NO_CXX11_HDR_FUNCTIONAL` to be compatible with C++03.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-04-15 22:08:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/99#issuecomment-483436855  

Actually, `hash_value` is already implemented. I've made my version in https://github.com/boostorg/uuid/pull/100.

---

## Comment 4

> Username: brad-anderson  
> Created_at: 2019-04-15 22:14:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/99#issuecomment-483438234  

Closing in favor of #100

---
