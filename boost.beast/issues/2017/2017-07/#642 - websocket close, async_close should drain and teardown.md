# #642 - websocket close, async_close should drain and teardown. [Closed]

> Username: vinniefalco  
> Created at: 2017-07-12 22:22:26 UTC  
> Updated at: 2017-08-05 15:37:00 UTC  
> Closed at: 2017-08-05 15:37:00 UTC  
> Url: https://github.com/boostorg/beast/issues/642  

To make things easy on callers we can have close and async close perform the drain and teardown. Other pending async operations can just complete with operation_aborted.
