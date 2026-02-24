# #211 - How to set Sec-WebSocket-Protocol on websocket client [Closed]

> Username: redboltz  
> Created at: 2016-12-22 07:23:49 UTC  
> Updated at: 2016-12-23 10:27:35 UTC  
> Closed at: 2016-12-23 10:27:35 UTC  
> Url: https://github.com/boostorg/beast/issues/211  

REF 6455 defines Sec-WebSocket-Protocol.  
https://tools.ietf.org/html/rfc6455#section-11.3.4  
  
I'm using MQTT broker that supports Websocket. It requires upgrade request from client need to have `Sec-WebSocket-Protocol` and that value is `mqtt`.  
  
I think that the timing of sending upgrade request is `async_handshake`.  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/async_handshake.html  
  
However, it seems that the interface doesn't support adding additional field such as Sec-WebSocket-Protocol.  
  
Is there any good way to do that?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-22 12:06:20 UTC  
> Updated at: 2016-12-22 12:06:30 UTC  
> Url: https://github.com/boostorg/beast/issues/211#issuecomment-268786593  

Yes, see https://github.com/vinniefalco/Beast/issues/70

---

## Comment 2

> Username: redboltz  
> Created at: 2016-12-23 10:27:35 UTC  
> Url: https://github.com/boostorg/beast/issues/211#issuecomment-268969670  

Thank you! I replaced `headers` with `fields` in your `set_subprotocols`, then it works fine :)
