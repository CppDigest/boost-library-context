# #1046 - websocket endpoints "ws://127.0.0.1:8080/76f48a44-0af8-444c-ba97-3f1ed34afc91/tweets" [Closed]

> Username: jdmairs  
> Created at: 2018-02-27 13:02:48 UTC  
> Updated at: 2018-03-02 17:42:35 UTC  
> Closed at: 2018-03-02 17:42:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1046  

I've gotten the websocket coroutine client/server examples to work.  It took me a little longer to figure out how to get the websocket client to talk to websocket.org since the webpage suggests connecting to ws://echo.websocket.org and the client program api took a host and ip.  
  
As shown in the title I've also seen urls that have endpoints other than root.  I tried to use the resolver and query objects but had no luck.    
  
Any quick advice on how to modify the client websocket coro example to ws:// style endpoints?  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-27 13:07:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1046#issuecomment-368867330  

Yes. "ws://" describes the **scheme** and is not part of the target but rather, informs the client of which protocol to use and also the default port. Since the port in your URL is specified (8080), it overrides the default WebSocket port. The "ws" scheme is unencrypted (compare with "wss://"). So, to connect you should first connect to 127.0.0.1 port 8080 and then perform the WebSocket handshake using the target "/76f48a44-0af8-444c-ba97-3f1ed34afc91/tweets".
