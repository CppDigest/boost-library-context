# #250 Missing #include in throw_exception.hpp [Merged]

> Username: jzmaddock  
> Created at: 2022-01-12 16:31:21 UTC  
> Updated at: 2024-08-19 21:07:52 UTC  
> Merged at: 2022-09-10 22:01:25 UTC  
> Closed at: 2022-09-10 22:01:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/250  

boost/serialization/throw_exception.hpp does not include the header which defines boost::throw_exception leading to compiler errors unless that header just happens to be #included elsewhere.  See this message thread: https://lists.boost.org/boost-users/2022/01/91144.php  
  
Likewise the std lib header <exception> is *not* required since we use nothing from that here.

---

## Review 1 [Commented]

> Username: uecasm  
> Created_at: 2022-01-12 22:20:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/250#pullrequestreview-851064180  

📁 include/boost/serialization/throw_exception.hpp

```diff
  18 | #include <boost/config.hpp>
  19 | 
  20 | #ifndef BOOST_NO_EXCEPTIONS
```

> Username: uecasm  
> Created_at: 2022-01-12 22:20:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/250#discussion_r783486350  

This needs to be an `#ifdef`, not an `#ifndef`.  Or just include it unconditionally since it contains a similar check internally.

> Username: jzmaddock  
> Created_at: 2022-01-13 11:50:08 UTC  
> Updated_at: 2022-01-13 11:50:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/250#discussion_r783886028  

You're right, but that still doesn't look right to me somehow :(


---
