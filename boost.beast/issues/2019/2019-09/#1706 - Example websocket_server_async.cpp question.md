# #1706 - Example websocket_server_async.cpp question [Closed]

> Username: gladiacxtylish  
> Created at: 2019-09-18 03:57:28 UTC  
> Updated at: 2019-09-18 06:36:20 UTC  
> Closed at: 2019-09-18 06:36:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1706  

I was reading the boost 1.66 websocket example [here](https://www.boost.org/doc/libs/1_66_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp). In the listener class, it initializes the socket_ object. And after the listener accepted the connection, in the on_accept function the socket_ is being **std::move** to the session object. I didn't see any logic to recreate the socket_ object. When the listener accepts another connection, would it be an issue for the next on_accept?

---

## Comment 1

> Username: djarek  
> Created at: 2019-09-18 05:02:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1706#issuecomment-532519373  

Sockets are guaranteed to be left in a usable state after they're moved-from(it's the same state as if the socket was just constructed with the 1-argument constructor).
