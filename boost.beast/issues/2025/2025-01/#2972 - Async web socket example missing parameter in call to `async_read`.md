# #2972 - Async web socket example missing parameter in call to `async_read` [Closed]

> Username: a-soll  
> Created at: 2025-01-17 23:12:58 UTC  
> Updated at: 2025-01-31 07:01:27 UTC  
> Closed at: 2025-01-31 07:01:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2972  

Was just working through [the example](https://github.com/boostorg/beast/blob/develop/example/websocket/client/async/websocket_client_async.cpp) and the call to `async_read` on line 130 is missing the web socket parameter. I wasn't able to compile without it.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-01-18 07:22:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2972#issuecomment-2599591200  

Line 130 in the linked example references `async_handshake`. Could you provide more details?

---

## Comment 2

> Username: a-soll  
> Created at: 2025-01-31 07:01:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2972#issuecomment-2626445778  

Hey, turns out I was using a different socket type than the example and hadn't noticed. This can be closed, apologies.
