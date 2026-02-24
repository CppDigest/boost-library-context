# #37 Remove linking with Boost.System [Merged]

> Username: Lastique  
> Created at: 2019-01-14 17:35:43 UTC  
> Updated at: 2019-01-16 22:22:41 UTC  
> Merged at: 2019-01-16 22:01:16 UTC  
> Closed at: 2019-01-16 22:01:16 UTC  
> Url: https://github.com/boostorg/chrono/pull/37  

Since Boost.System is now header-only, no need to link with the library.

---

## Comment 1

> Username: viboes  
> Created_at: 2019-01-14 21:37:48 UTC  
> Url: https://github.com/boostorg/chrono/pull/37#issuecomment-454170981  

Boost.System is header only since a long time.  
But, Boost.System is also a library providing a binary.  
  
What is the problem?  
Why it is better to manage it as header only?

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-01-15 09:29:03 UTC  
> Url: https://github.com/boostorg/chrono/pull/37#issuecomment-454324228  

Linking with the library adds a runtime dependency on the Boost.System binary, which is no longer needed. For example, in Debian it will cause libboost-thread package dependency on libboost-system, and will cause that unneeded library to be loaded into processes.

---

## Comment 3

> Username: viboes  
> Created_at: 2019-01-16 22:01:44 UTC  
> Url: https://github.com/boostorg/chrono/pull/37#issuecomment-454960783  

As the Boost.System library is now empty there is no reason to link with.

---
