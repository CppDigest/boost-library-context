# #1456 - websocket stream check state and invariants [Closed]

> Username: vinniefalco  
> Created at: 2019-02-16 03:53:15 UTC  
> Updated at: 2022-06-16 16:22:03 UTC  
> Closed at: 2022-06-16 16:22:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1456  

asynchronous websocket operations should make a best effort to deliver an error if they are misused.  
  
For example, calling `async_write` before `async_handshake` completes should cause the write to complete immediately with some kind of logic error.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-03-18 05:13:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1456#issuecomment-473772533  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 16:22:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1456#issuecomment-1157870621  

We aren't doing this anymore
