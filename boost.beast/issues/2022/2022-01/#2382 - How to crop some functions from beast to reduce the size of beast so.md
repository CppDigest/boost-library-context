# #2382 - How to crop some functions from beast to reduce the size of beast so? [Closed]

> Username: zaley2000  
> Created at: 2022-01-21 08:15:01 UTC  
> Updated at: 2022-06-14 17:38:20 UTC  
> Closed at: 2022-06-14 17:38:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2382  

the so size in linux is too big, close to 70M. Can some functions of beast be tailored, such as HTTP. We only use websocket

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-01-21 08:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2382#issuecomment-1018279154  

This is a release build with symbols stripped and `NDEBUG` defined?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-01-21 23:39:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2382#issuecomment-1018960489  

Beast is header-only by default, how are you getting a shared library?
