# #1081 - websocket cannot connect to www.bitmex.com [Closed]

> Username: youngkylejan  
> Created at: 2018-03-29 12:40:22 UTC  
> Updated at: 2018-03-29 13:45:26 UTC  
> Closed at: 2018-03-29 13:45:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1081  

bitmex websocket api: https://www.bitmex.com/app/wsAPI  
  
use the websocket_client_async_ssl example to start the connection to www.bitmex.com  
  
but it always handshake failed. it returns "unknown protocol" or "wrong version number" error message

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-29 13:00:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1081#issuecomment-377227590  

Well what do the HTTP headers that come back look like?

---

## Comment 2

> Username: youngkylejan  
> Created at: 2018-03-29 13:12:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1081#issuecomment-377230686  

how  to get the http headers before async_handshake in beast?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-03-29 13:17:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1081#issuecomment-377231844  

Use the overload of `async_handshake` which fills out the response:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/handshaking_clients.html#beast.using_websocket.handshaking_clients.filtering  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_handshake/overload2.html

---

## Comment 4

> Username: youngkylejan  
> Created at: 2018-03-29 13:21:56 UTC  
> Updated at: 2018-03-29 13:36:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1081#issuecomment-377233099  

oh sorry. i am not describe the problem clearly. i fail on the websocket::stream::ssl_stream::async_handshake, which use to do the ssl handshake.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-03-29 13:36:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1081#issuecomment-377237356  

Oh...well, I have no idea why that would fail. Perhaps contact BitMEX? Or look up common reasons why SSL handshakes produce those errors:  
http://blog.techstacks.com/2010/03/3-common-causes-of-unknown-ssl-protocol-errors-with-curl.html

---

## Comment 6

> Username: youngkylejan  
> Created at: 2018-03-29 13:45:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1081#issuecomment-377239841  

gotcha, thx!
