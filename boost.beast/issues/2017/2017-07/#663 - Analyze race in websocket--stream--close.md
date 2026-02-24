# #663 - Analyze race in websocket::stream::close [Closed]

> Username: vinniefalco  
> Created at: 2017-07-17 13:31:01 UTC  
> Updated at: 2017-08-16 00:01:38 UTC  
> Closed at: 2017-08-16 00:01:38 UTC  
> Url: https://github.com/boostorg/beast/issues/663  

It might be possible for the implementation to respond automatically with a close frame, at the same time or just before the application is trying to call `async_close`. There is an assert in `close` which is not present in `async_close`. It might be necessary to `async_close` to also assert. Or, `close` should not assert. The close code needs a refactoring.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-04 23:02:38 UTC  
> Url: https://github.com/boostorg/beast/issues/663#issuecomment-320371177  

The code is refactored now it needs analysis

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-16 00:01:00 UTC  
> Url: https://github.com/boostorg/beast/issues/663#issuecomment-322621450  

I believe it is correct now
