# #637 - Add websocket::error::closed_dirty [Closed]

> Username: vinniefalco  
> Created at: 2017-07-11 17:42:45 UTC  
> Updated at: 2017-09-01 03:26:20 UTC  
> Closed at: 2017-09-01 03:26:20 UTC  
> Url: https://github.com/boostorg/beast/issues/637  

When a websocket stream receives a close frame from the other end, the teardown may fail due to a non-graceful closure. Currently the caller sees `error::failed` which is unhelpful. Instead, it should generate `error::closed_dirty`. This code indicates that a correct close frame was received (and accessible by calling `stream::reason`), but the teardown caused an error. This is likely due to the other end doing an abortive close instead of cleanly shutting down.  
  
Motivated by #636

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:26:20 UTC  
> Url: https://github.com/boostorg/beast/issues/637#issuecomment-326477994  

Changed my mind
