# #934 - define custom STATIC_ASSERT macro [Closed]

> Username: vinniefalco  
> Created at: 2025-10-21 18:03:49 UTC  
> Updated at: 2025-10-22 18:09:22 UTC  
> Closed at: 2025-10-22 16:05:48 UTC  
> Url: https://github.com/boostorg/url/issues/934  

We should not depend on Boost.StaticAssert, and instead just define our own macro in detail/config.hpp

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-10-22 18:09:22 UTC  
> Url: https://github.com/boostorg/url/issues/934#issuecomment-3433596230  

Sorry, I did not know that Peter added `BOOST_CORE_STATIC_ASSERT`. If a library already uses Core, it might as well use that macro instead of defining its own.
