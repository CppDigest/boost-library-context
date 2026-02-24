# #332 - Remove websocket::keep_alive option [Closed]

> Username: vinniefalco  
> Created at: 2017-05-01 21:34:03 UTC  
> Updated at: 2017-05-08 00:04:36 UTC  
> Closed at: 2017-05-08 00:04:36 UTC  
> Url: https://github.com/boostorg/beast/issues/332  

We can get rid of this option and just let callers set keep-alive in requests and responses using decorators. The implementation can detect if the decorator wants keep-alive and otherwise close the connection using teardown if desired.
