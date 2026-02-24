# #1709 - Websocket fails at handshake if you send too much data [Closed]

> Username: rraggerr  
> Created at: 2019-09-24 17:28:18 UTC  
> Updated at: 2019-09-24 17:40:46 UTC  
> Closed at: 2019-09-24 17:40:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1709  

Hello, im trying to transfer my large data while connecting to the server, but everything i have check(cookies, protocols, etc) gives exception at handshake.  
  
You can reproduce this error if change this:  
https://github.com/boostorg/beast/blob/develop/example/websocket/client/sync/websocket_client_sync.cpp#L67  
to:  
`string _s;  
			_s.resize( 500 ); // you can manually input a string, but result still same`  
`  
			req.set( http::field::user_agent /* for example */ , _s );`  
  
Exception shows : Error: The WebSocket handshake was declined by the remote peer

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-24 17:29:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1709#issuecomment-534663766  

The server is rejecting your handshake, this is not the fault of Beast. You would have to inspect the server to know exactly why it is rejecting the handshake.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-09-24 17:31:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1709#issuecomment-534664413  

Actually if you do `_s.resize(500)` doesn't that leave you with 500 NULL characters? Nulls are not allowed in HTTP field values, so this would definitely be a problem for the remote peer.

---

## Comment 3

> Username: rraggerr  
> Created at: 2019-09-24 17:40:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1709#issuecomment-534668428  

> Actually if you do `_s.resize(500)` doesn't that leave you with 500 NULL characters? Nulls are not allowed in HTTP field values, so this would definitely be a problem for the remote peer.  
  
Yes, you right. Solved problem. HTTP cant handle NULLs. Just erasing all '\n' symbols and it fixes my problem.
