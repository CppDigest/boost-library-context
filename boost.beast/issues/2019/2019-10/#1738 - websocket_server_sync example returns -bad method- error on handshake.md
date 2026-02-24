# #1738 - websocket_server_sync example returns "bad method" error on handshake [Closed]

> Username: SebMeunier  
> Created at: 2019-10-20 15:10:30 UTC  
> Updated at: 2019-11-20 19:08:43 UTC  
> Closed at: 2019-11-20 19:08:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1738  

Hi!   
  
### Version of Beast: 266  
  
### All relevant compiler information: Using Visual Studio 2017  
  
### Steps necessary to reproduce the problem  
  
I just used the sample websocket_server_sync.cpp and tried to connect from https://www.websocket.org/echo.html  
  
I ran the server code with `127.0.0.1 2974` as arguments from VS debugger  
  
I tried to connect from https://www.websocket.org/echo.html with either:   
  
* wss://127.0.0.1:2974  
* wss://127.0.0.1:2974/  
* wss://127.0.0.1:2974/Echo  
  
I always get a "Bad method" system_error at the ws.accept();  
  
I also tried with a C# client I have written that works with another WS server, I get this error log on the client side:   
  
`Error|WebSocket.doHandshake|Includes an invalid Sec-WebSocket-Protocol header.`  
  
Client code  
  
I am not a C++ developer (anymore) but I need to create a simple websocket server that will send a message to connected client(s)   
  
I might be missing something obvious in the server configuration, such as the "target name"? or "service name"? but some help would appreciated.  
  
Thanks.  
  
Seb

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-20 15:31:48 UTC  
> Updated at: 2019-10-20 15:32:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1738#issuecomment-544262988  

Sounds like you are using a non-SSL server and trying to accept SSL clients. Try using this example instead: https://github.com/boostorg/beast/tree/develop/example/websocket/server/sync-ssl

---

## Comment 2

> Username: SebMeunier  
> Created at: 2019-10-21 16:43:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1738#issuecomment-544602687  

Hi @vinniefalco   
  
Thanks for the quick answer. Removing the wss part in the JS client definitely helped to make the connection, as I don't really need SSL here.   
  
Since I'm here, you might be able to give me some advice on the best approach for my use case:   
  
* I have a C++ app that does a few things and that will host the WS server side.   
* On the same machine or on a local network, I'll have a client (C# or JS) that will connect to this WS server.   
* I need the server to send text (JSON) messages to all connected clients  
* 99% of the case there will be only 1 client connected.   
  
As I embedded the server sync code in a class but the "session" is created in a thread, I'm not sure how I can keep a reference on the opened connections/sockets to be able to write my messages from my class.  
  
Thanks for any help / suggestion you may have here.   
  
Seb

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-21 17:54:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1738#issuecomment-544631919  

This example implements broadcasting:  
https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-11-20 17:55:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1738#issuecomment-556173094  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: SebMeunier  
> Created at: 2019-11-20 19:08:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1738#issuecomment-556259925  

Sorry for the late news. Thanks again @vinniefalco  for your help, it works like a charm with your last sample.
