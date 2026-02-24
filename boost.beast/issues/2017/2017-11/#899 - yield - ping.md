# #899 - yield / ping [Closed]

> Username: rberlich  
> Created at: 2017-11-22 10:46:14 UTC  
> Updated at: 2017-11-29 04:09:47 UTC  
> Closed at: 2017-11-29 04:09:47 UTC  
> Url: https://github.com/boostorg/beast/issues/899  

Hi there,  
  
do I understand it correctly that in this example: http://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/coro/websocket_client_coro.cpp , if I would run a for(;;)-loop in do_session() and send/receive repeatedly, having yields in regular intervals in the loop is essential to keep the background-ping alive ?  
  
The use-case is a client that repeatedly asks for work from the server; processing of this work may then take anything from seconds to days, and the processing is not able to yield.   
  
So running such workloads would require processing to be done asynchronously outside of the loop, e.g. via std::async, some threadpool or via ASIO asynchronous operations.  
  
Thanks and Kind Regards,  
Beet

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-22 12:12:50 UTC  
> Updated at: 2017-11-22 12:18:01 UTC  
> Url: https://github.com/boostorg/beast/issues/899#issuecomment-346333014  

There's no "background ping" in beast. If you want beast to send a ping you have to explicitly request it (by calling `ping` or `async_ping`). Beast will automatically respond to incoming pings with a pong. This behavior is implemented in the `read` and `async_read` operations. In order for beast to read and respond to an incoming ping, a read operation must be active. Beast can also send an unsolicited pong to inform the remote host that the application is still alive during long-running operations,  simply call `pong` or `async_pong`.  
  
This is described in the documentation:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_websocket/control_frames.html  
  
Beast doesn't do anything "in the background". It doesn't set timers and it doesn't create any threads. Any beast behaviors are the result of synchronous or asynchronous operations initiated by the caller.  
  
>running such workloads would require processing to be done asynchronously outside of the loop  
  
Blocking a thread calling `io_service::run` for long periods of time is not advised. Such work should be performed on a separate thread, as you described.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-11-22 12:13:24 UTC  
> Url: https://github.com/boostorg/beast/issues/899#issuecomment-346333143  

The websocket servers examples should probably implement background pings

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-11-28 00:46:51 UTC  
> Url: https://github.com/boostorg/beast/issues/899#issuecomment-347376895  

In the next version (147):  
  
The timer logic for the advanced-server and advanced-server-flex examples is refactored to use idle pings when the connection has not seen activity for some period of time. This demonstrates the use of the stream's control_callback interface.

---

## Comment 4

> Username: rberlich  
> Created at: 2017-11-28 09:46:43 UTC  
> Url: https://github.com/boostorg/beast/issues/899#issuecomment-347468773  

Cool — thanks a lot!  
  
Beet  
  
> Am 28.11.2017 um 01:46 schrieb Vinnie Falco <notifications@github.com>:  
>   
> In the next version (147):  
>   
> The timer logic for the advanced-server and advanced-server-flex examples is refactored to use idle pings when the connection has not seen activity for some period of time. This demonstrates the use of the stream's control_callback interface.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/899#issuecomment-347376895>, or mute the thread <https://github.com/notifications/unsubscribe-auth/ASlWX__saQyMQoVBw6xBMnKFKvT5vCN7ks5s61f_gaJpZM4QnMsl>.  
>
