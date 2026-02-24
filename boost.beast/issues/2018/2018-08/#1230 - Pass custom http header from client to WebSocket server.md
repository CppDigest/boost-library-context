# #1230 - Pass custom http header from client to WebSocket server. [Closed]

> Username: JunielKatarn  
> Created at: 2018-08-18 02:40:33 UTC  
> Updated at: 2018-09-17 21:13:51 UTC  
> Closed at: 2018-09-17 21:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1230  

### Version of Beast  
144 (Boost 1.66)  
  
### Question  
Is there a way for a Beast websocket server to read/validate client-provided custom headers?  
This question would seem to have an answer in https://github.com/boostorg/beast/issues/501, but the links seem outdated.  
  
I'm aware of the API described here https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html, but can't make sense of how the client-set headers would be passed around the remote server endpoint.  
  
I intend to set such custom headers as described here:  
https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/handshaking_clients.html#beast.using_websocket.handshaking_clients.decorators

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-18 03:31:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1230#issuecomment-414029115  

> Is there a way for a Beast websocket server to read/validate client-provided custom headers?  
  
Yes. See:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html#beast.using_websocket.handshaking_servers.passing_http_requests  
  
Also:  
https://github.com/boostorg/beast/blob/1da229a27c6f0539d422bcedbcf47cfe2517164a/example/advanced/server/advanced_server.cpp#L658  
  
Note: If you are going to use the examples from 1.68 you will also need to use Boost 1.68. I suggest you use the latest version of Boost, since it contains some important fixes to Beast.

---

## Comment 2

> Username: JunielKatarn  
> Created at: 2018-08-18 03:37:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1230#issuecomment-414029384  

Thanks. I'll give it a try.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-09-17 03:45:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1230#issuecomment-421884666  

This issue has been open for a while with no activity, has it been resolved?
