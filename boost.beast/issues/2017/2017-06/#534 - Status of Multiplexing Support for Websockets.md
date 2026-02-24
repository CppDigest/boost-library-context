# #534 - Status of Multiplexing Support for Websockets [Closed]

> Username: solvingj  
> Created at: 2017-06-23 21:38:04 UTC  
> Updated at: 2017-06-23 23:47:24 UTC  
> Closed at: 2017-06-23 23:47:24 UTC  
> Url: https://github.com/boostorg/beast/issues/534  

This is a request for the status of Multiplexing support.  I saw discussion in #121 that Beast was "designed with HTTP/2 in mind".  Also "The scope of HTTP/2 is much bigger than HTTP/1, covering things like flow control and multiplexing."    
  
I believe multiplexing must be on the roadmap, but it's just a matter of when.  My team has a need for this so I'm wondering if it's been looked into at all, or if anyone is actively developing it already.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-23 21:49:41 UTC  
> Url: https://github.com/boostorg/beast/issues/534#issuecomment-310780825  

Are we talking WebSocket or HTTP? What exactly do you mean by "multiplexing?"

---

## Comment 2

> Username: solvingj  
> Created at: 2017-06-23 22:25:20 UTC  
> Updated at: 2017-06-23 22:25:45 UTC  
> Url: https://github.com/boostorg/beast/issues/534#issuecomment-310786234  

Here is a good link describing the two options:   
https://hpbn.co/websocket/#websocket-multiplexing-and-head-of-line-blocking  
  
Our goal is to effectively distinguish between multiple conversations taking place over a single connection.  Our conversations will be TCP streams coming from ASIO (rather than "typical" http data.  According to that article, it looks like that can be using a websocket which was our original intention (but which would require the implementation of such an extension), or HTTP/2 framing which I am about to read up on now. While HTTP/2 tagging might natively distinguish multiple conversations over 1 TCP connection, I don't know if it's a good fit for streaming TCP sessions over.  Thoughts?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-23 22:58:30 UTC  
> Url: https://github.com/boostorg/beast/issues/534#issuecomment-310790678  

The problem with the multiplexing draft is that it expired and was never adopted. So there is no "spec" to work from. If you decide to use WebSockets, you will have to define your own multiplexing protocol and implement it at both ends. WebSocket is considerably easier than HTTP/2.

---

## Comment 4

> Username: solvingj  
> Created at: 2017-06-23 23:01:53 UTC  
> Updated at: 2017-06-23 23:02:23 UTC  
> Url: https://github.com/boostorg/beast/issues/534#issuecomment-310791144  

Yes it's a bit disappointing about websocket multiplexing not being adopted. This is why I am hoping for HTTP/2 to be a good fit. The session tagging is inherent and should be fully supported in any HTTP/2 impl.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-23 23:06:01 UTC  
> Url: https://github.com/boostorg/beast/issues/534#issuecomment-310791622  

HTTP/2 is very complicated and few implementations exist. You might consider implementing your own version of multiplexing on top of WebSocket. If you design suitable interfaces you could change the implementation later without disturbing calling code too much.

---

## Comment 6

> Username: solvingj  
> Created at: 2017-06-23 23:25:35 UTC  
> Url: https://github.com/boostorg/beast/issues/534#issuecomment-310794045  

Thanks for the tips, there are already a few implementations of something similar which serve as a reference for us, but I'm not much for defining and implementing single-use network protocols from scratch if I can avoid it.  It feels like we're being forced into a lose/lose situation... a situation I find myself in often.  The new way (HTTP/2) is harder and/or not quite ready, and the old way has a lot more DIY and debugging associated.  However, the worst part about deciding to DIY with an old way, is that you have to keep facing the notion of: "HTTP/2 does this for you, why are you reinventing the wheel... and why are you using the old way...".  It's hard to argue with this notion, and it's usually right. Will take more research I think.   
  
Question for you:  Is Beast's HTTP2 implementation ready for us to do something like this with it, if that's the route we decide to go?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-23 23:46:29 UTC  
> Url: https://github.com/boostorg/beast/issues/534#issuecomment-310796222  

There is zero support for HTTP/2 in Beast right now. The only thing that will be used from the current library will be the `message` container and `basic_fields`. HTTP/2 requires completely new algorithms and implementation, and can't use free functions, serializers, and parsers the way that Beast's HTTP/1 does. HTTP/2 is on the roadmap but given the post-acceptance workload (getting Beast to work on all of Boost's supported platforms, and the inevitable bugs which will pop up) it is unlikely that I will be able to even start until this time next year, roughly.

---

## Comment 8

> Username: solvingj  
> Created at: 2017-06-23 23:47:24 UTC  
> Url: https://github.com/boostorg/beast/issues/534#issuecomment-310796331  

Great to know, thanks very much for the update and the project!
