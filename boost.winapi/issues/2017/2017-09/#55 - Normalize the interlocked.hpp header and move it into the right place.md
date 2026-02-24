# #55 - Normalize the interlocked.hpp header and move it into the right place [Closed]

> Username: jeking3  
> Created at: 2017-09-16 11:54:53 UTC  
> Updated at: 2017-09-16 23:20:08 UTC  
> Closed at: 2017-09-16 14:51:46 UTC  
> Url: https://github.com/boostorg/winapi/issues/55  

This header appears to have been one of the earliest in the suite.  It does not follow the flow of the rest of the winapi headers and should be refactored to fit in, and also moved into the right place.  To maintain backwards compatibility, a deprecation warning header can be left in its place for one release (and another issue should then be filed to remove it in the following release).

---

## Comment 1

> Username: Lastique  
> Created at: 2017-09-16 14:51:46 UTC  
> Url: https://github.com/boostorg/winapi/issues/55#issuecomment-329973166  

interlocked.hpp was historically not part of Boost.WinAPI and in fact does not define WinAPI functions. I'm not convinced it should be moved inside boost/detail/winapi.

---

## Comment 2

> Username: jeking3  
> Created at: 2017-09-16 19:13:26 UTC  
> Url: https://github.com/boostorg/winapi/issues/55#issuecomment-329988975  

Where should it go? :)

---

## Comment 3

> Username: Lastique  
> Created at: 2017-09-16 23:20:08 UTC  
> Url: https://github.com/boostorg/winapi/issues/55#issuecomment-330000793  

It's fine where it is, IMHO. Another place could be Boost.Atomic, but it's not perfect either because Boost.Atomic is not about providing Windows-specific macros. Also, it would change the dependency graph and would have to be discussed on the ML. Basically, Boost.WinAPI is the place that makes most sense currently and doesn't impose many more dependencies on its users.
