# #73 - define custom STATIC_ASSERT macro [Closed]

> Username: vinniefalco  
> Created at: 2025-10-21 18:04:27 UTC  
> Updated at: 2025-10-22 17:07:55 UTC  
> Closed at: 2025-10-22 17:07:55 UTC  
> Url: https://github.com/boostorg/static_string/issues/73  

We should not depend on Boost.StaticAssert, and instead just define our own macro in detail/config.hpp

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-10-22 10:21:49 UTC  
> Url: https://github.com/boostorg/static_string/issues/73#issuecomment-3431599905  

It seems that we are not using static asserts at all.
