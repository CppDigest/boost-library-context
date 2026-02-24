# #1117 - Canceling websocket async operations and status of websocket connection [Closed]

> Username: shuras109  
> Created at: 2018-05-06 14:42:00 UTC  
> Updated at: 2018-05-06 19:01:34 UTC  
> Closed at: 2018-05-06 16:27:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1117  

Is there any way to cancel async operations and be able to do async_close for websocket connection later?  
 With current 1.67 boost beast I've tried to do something like that: "ws_.next_layer().cancel();". As soon as websocket  async_read finished with operation_aborted error websoket connection open status become "false" while open status of underlying socket is still "true". And if I try to do async_close later ws connection closed instantly without graceful websocket shutdown (reason wasn't send).  
  Is there anyway else to do it? I just want to drop all pending async operations and do some other operations before closing websocket connection. Currently I am coerce to keep pending async ops and just ignore them if some of them complete.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-06 15:56:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1117#issuecomment-386889501  

>Is there any way to cancel async operations and be able to do async_close for websocket connection later?  
  
In general, no. The problem is that canceling pending I/O could leave the stream in an inconsistent state. For example, the implementation may have just finished sending a websocket frame with the continuation bit set. At that point, it would be a protocol error to attempt sending a new message. When a WebSocket read operation receives a close frame, it initiates the closing procedure automatically. If you cancel this I/O, then again the stream could be in an inconsistent state.  
  
All of this is equally applicable to canceling I/O for an `ssl::stream`, even when not using `websocket::stream`.  
  
> I just want to drop all pending async operations and do some other operations before closing websocket connection  
  
Which pending operations do you want to cancel, and what other operations do you want to do before closing?

---

## Comment 2

> Username: shuras109  
> Created at: 2018-05-06 16:27:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1117#issuecomment-386891441  

Got it. Thanks a lot.   
  Normally I just want to cancel websocket async_reads which can be processed at the moment of arriving stop event from another thread. And mentioned operations is just communications with other threads and services to inform of websocket connection termination. Those communication can take a lot of time, during which I want to halt all ws messages exchange.   
  But anyway it is not such big deal to receive message with last remaining async_read() and just ignore it. Probably all packet still should be read before websocket closing frames can be processed.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-06 16:46:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1117#issuecomment-386892624  

If you have a pending call to `async_read`, and you call `async_close`, a close frame will be sent to the remote peer, and then one of two things will happen:  
  
1. If the `async_read` is in the middle of receiving a message frame, it will finish reading the message and invoke the completion handler. Then the `async_close` operation will read the close frame from the remote peer, and the connection will be closed. The completion handler for the `async_close` will be invoked  
  
2. If `async_read` has not received any bytes corresponding to a new message frame, it will read the close frame from the remote peer, and the error `websocket::error::closed` will be delivered to the completion handler. Then the connection will be closed.  
  
In both cases, the connection is closed. Therefore, if you want to stop the websocket stream you should simply call `async_close`, possibly discarding any message data received in a subsequent invocation of the read completion handler.

---

## Comment 4

> Username: shuras109  
> Created at: 2018-05-06 18:42:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1117#issuecomment-386902808  

Thank you very much.   
  I actually get a question for another scenario:  
1) on my side i finish reading frames (last async_read() completed and in the handler I don't issue new async_read() )  
2) peer sent me a few more messages and it was received by tcpip stack on my side (those bytes must be in the OS buffers)  
3) at that moment I still have some unread messages but don't care about them and call async_close()  
  
Will async_close() deals with this situation correctly? I suppose it will read and discard those messages (other way it will not get peers websocket connection closing frame). Just ask to be sure.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-06 19:01:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1117#issuecomment-386904361  

> Will async_close() deals with this situation correctly?  
  
Yes:  
https://github.com/boostorg/beast/blob/46e658a91bbda276d674a27c71ca56002b656817/include/boost/beast/websocket/impl/close.ipp#L367
