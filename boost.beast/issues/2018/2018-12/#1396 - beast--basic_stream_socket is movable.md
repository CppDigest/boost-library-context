# #1396 - beast::basic_stream_socket is movable [Closed]

> Username: vinniefalco  
> Created at: 2018-12-30 08:23:48 UTC  
> Updated at: 2019-02-02 19:46:57 UTC  
> Closed at: 2019-02-02 19:46:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1396  

Since the implementation is in a shared pointer, it should be possible to move the object while there are operations outstanding.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-01-29 10:47:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1396#issuecomment-458494610  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-02 19:46:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1396#issuecomment-459993725  

This needs more research
