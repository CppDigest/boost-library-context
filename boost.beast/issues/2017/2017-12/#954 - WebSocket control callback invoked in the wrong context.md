# #954 - WebSocket control callback invoked in the wrong context [Closed]

> Username: vinniefalco  
> Created at: 2017-12-27 16:48:40 UTC  
> Updated at: 2017-12-31 17:41:09 UTC  
> Closed at: 2017-12-31 17:41:09 UTC  
> Url: https://github.com/boostorg/beast/issues/954  

When the internal read buffer contains a control frame and `stream::async_read_some` is called, it is possible for the control callback to be invoked on the caller's stack instead of through the executor associated with the final completion handler.
