# #38 - Reconnect behavior with WebSockets & SSL [Closed]

> Username: seiband  
> Created at: 2025-10-16 11:51:03 UTC  
> Updated at: 2025-11-06 09:35:10 UTC  
> Closed at: 2025-11-06 09:35:10 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/38  

Hi all,  
  
I've been running into an issue when trying to connect to a broker (mosquitto 2.0.19) using websockets & ssl.   
The initial connection to the mqtt broker works fine and I can also observe logs on both client and broker.  
If the connection drops (e.g. broker is shutdown), the client doesn't automatically reconnect to the broker and no further logs are visible.  
All other combinations (tcp without ssl, tcp with ssl, websockets without ssl) are working as expected.  
  
**Reproduction steps**  
  
1. Instantiate mqtt client with websocket & ssl  
```  
boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>  
```  
2. Start client and call `async_run`  
3. Start MQTT broker with compatible configuration  
4. Wait for successful client connection  
5. Stop MQTT broker  
6. Completion handler of `async_run` is executed with error code `operation_canceled`  
7. Client doesn't try to reconnect  
  
**Expected behavior**  
The client reconnects automatically independent of the underlying transport layers and the completion handler of `async_run` is not called.  
At least logs should be there to indicate errors in the underlying transport layers.  
  
**Actual behaviour**  
The client doesn't reconnect automatically after the first successful connection. In fact, the completion handler of `async_run` is called with error code `operation_canceled`.  
  
**Analysis**  
As mentioned in the introduction I've already tested various transport configurations and this issue only exists for websockets & ssl.  
I've also debugged my application when the completion handler of `async_run` is fired.   
The expression `_svc_ptr->is_open()` evaluates to false in one of the files (`sentry_op.hpp`) which leads to the execution of the completion handler.   
Does the combination of websockets and ssl behave differently here?  
  
**Workaround**  
If I call `async_run` again from within the completion handler, I can get the client to start connecting to the broker again.  
(whether that's a good or bad idea is probably another discussion...)  
  
Best regards,  
Stefan

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-10-20 15:53:10 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/38#issuecomment-3422694724  

Hi,  
  
Mosquitto broker doesn't send `close_notify` when using WebSocket over TLS, it just closes the underlying socket. That results in `asio::ssl::stream_truncated` error on `async_read_some` which wasn't in our list of *reconnectable* error codes.   
  
I've updated the logic so we now reconnect on all transport layer errors and also log them via the `at_transport_error` callback. Fix is on develop branch for now.  
  
Thanks for the detailed report!  
  
>  If I call async_run again from within the completion handler, I can get the client to start connecting to the broker again.  
(whether that's a good or bad idea is probably another discussion...)  
  
That is a valid usage of the client, see https://www.boost.org/doc/libs/develop/libs/mqtt5/doc/html/mqtt5/disconnecting_the_client.html#mqtt5.disconnecting_the_client.reusing_the_client. But it shouldn't ever be necessary with the fix.

---

## Comment 2

> Username: seiband  
> Created at: 2025-10-23 12:11:11 UTC  
> Updated at: 2025-10-23 12:15:02 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/38#issuecomment-3436601052  

I've just noticed that we also receive `operation_aborted` instead of `session_expired` on `async_receive()`. Does your commit also handle this scenario correctly? Unfortunately, I can't try this out right now as I'm stuck with an older release.

---

## Comment 3

> Username: biljazovic  
> Created at: 2025-10-23 12:24:17 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/38#issuecomment-3436654723  

This happened because when an *unreconnectable* transport error occured, the client would stop entirely, cancelling all operations, including `async_run` and `async_receive`.  
With the fix, this will only happen when you explicitly cancel it, e.g. by calling `cancel` or `async_disconnect`.
