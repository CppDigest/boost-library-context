# #271 - Allow simultaneous async ping and write (WebSocket) [Closed]

> Username: vinniefalco  
> Created at: 2017-02-23 23:42:39 UTC  
> Updated at: 2017-02-27 17:09:00 UTC  
> Closed at: 2017-02-27 17:09:00 UTC  
> Url: https://github.com/boostorg/beast/issues/271  

Implementing a timeout on a websocket server requires writing burdensome, error-prone code because of the restriction that `websocket::stream::async_ping` and `websocket::stream::async_write_frame` cannot be called concurrently. With some work and careful manipulation of state, we should relax this restriction.
