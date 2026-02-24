# #2711 - How does a client ping server proactively and periodically? [Closed]

> Username: zptan  
> Created at: 2023-08-03 02:36:34 UTC  
> Updated at: 2023-08-09 06:15:43 UTC  
> Closed at: 2023-08-09 06:15:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2711  

My app is a client that subscribes data from a server, the server requires the client to ping periodically (e.g., every 15s), and the ping payload must be in a specific format (so the automatic ping does not work), or the connection will be ended by the server.  
  
A common solution: create a thread that periodically send a ping. But I read some posts and found: _**websocket does not support multi-threading**_.  
  
If the data is pushed frequently by the server, we can ping after reading the data (passive ping), but it is not a general case.  
  
So how does a client do these jobs in a single thread?  
- Receive data from the server  
- Ping the the server periodically with specific payload  
  
### Version of Beast  
Beast 300  
Boost 1.74

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-03 02:58:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2711#issuecomment-1663222650  

By using the `async_*` APIs. You will find plenty examples in the repository and you should have a look at asio::steady_timer.

---

## Comment 2

> Username: zptan  
> Created at: 2023-08-03 03:06:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2711#issuecomment-1663227112  

Oh, `async_*` APIs are thread-safe? That's great, thanks!

---

## Comment 3

> Username: zptan  
> Created at: 2023-08-03 03:22:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2711#issuecomment-1663236328  

From [doc](https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/send_and_receive_messages.html)  
> [websocket::stream](https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream.html) is not thread-safe. Calls to stream member functions must all be made from the same implicit or explicit strand.  
  
> `async_*` APIs are thread-safe  
  
I'm not sure...

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-08-03 04:19:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2711#issuecomment-1663267641  

No they're not thread-safe either. But they're non-blocking so you can have multiple operations going on at the same time.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-08-03 04:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2711#issuecomment-1663268171  

What exactly do you want to do? Trigger a message periodically? Look into asio::steady_timer then. You .async-wait the thing and then trigger what you need to do.

---

## Comment 6

> Username: zptan  
> Created at: 2023-08-03 06:36:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2711#issuecomment-1663374292  

> What exactly do you want to do? Trigger a message periodically?   
  
Yes, the server requires the client to ping (with payload) periodically, e.g., every 15s  
  
The client must ping even it has not received data for a long time, or the server will end the connection.  
  
> Look into asio::steady_timer then. You .async-wait the thing and then trigger what you need to do.  
  
Thanks, will do.

---

## Comment 7

> Username: zptan  
> Created at: 2023-08-09 06:15:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2711#issuecomment-1670735115  

@klemens-morgenstern That's very helpful, thx!
