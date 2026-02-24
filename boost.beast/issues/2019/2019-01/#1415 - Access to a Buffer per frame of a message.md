# #1415 - Access to a Buffer per frame of a message [Closed]

> Username: jhmmufc  
> Created at: 2019-01-17 19:51:55 UTC  
> Updated at: 2019-01-17 20:11:48 UTC  
> Closed at: 2019-01-17 20:11:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1415  

I am currently working on some websocket server and client code. My protocol relies on a header being sent and then n additional frames depending on the message type.  
  
Is it possible using beast to read each individual frame into its own buffer so that I do not need parse multiple frames out a single buffer. I was hoping that multibuffer would do this but it does not seem to store each frame individually as I would maybe expect.  
  
I understand I could do read_some into a buffer and then read some more etc. but the clients allow me to send individual frames as well as marking the last frame and it would be nice if on the server side I could handle these frames individually.   
  
It is harder because I do not always easily know the size of each additional message part and therefore have to increase information in the header or add additional metadata that is already available at a websocket frame level.   
  
Any help is much appreciated and apologies if I am missing something simple.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-17 20:04:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1415#issuecomment-455313177  

> My protocol relies on a header being sent and then n additional frames depending on the message type.  
  
It isn't really possible to do this portably with WebSocket, because according to the specification (https://tools.ietf.org/html/rfc6455) intermediates such as proxies (or other software layers such as beast) can re-frame messages however they like. For example, the sender might emit a complete message, but a proxy along the way may decide to break that message up into 1KB frames. Or the opposite can happen, a message may be sent as multiple frames but an intermediate computer could combine those frames into one.  
  
Because of this, Beast does not provide a way to interact with websocket connections directly at the frame level, so it will generally not be possible to support the protocol that you describe. My advice is that you change your protocol to not depend on the way that messages are framed.  
  
A simple analogy is TCP/IP. Applications written for TCP/IP should never depend on how streams are broken up into individual IP frames and should instead treat the stream as a series of ordered bytes.

---

## Comment 2

> Username: jhmmufc  
> Created at: 2019-01-17 20:11:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1415#issuecomment-455315475  

Fair enough. Thanks for the answer. I will reevaluate my protocol. Thanks
