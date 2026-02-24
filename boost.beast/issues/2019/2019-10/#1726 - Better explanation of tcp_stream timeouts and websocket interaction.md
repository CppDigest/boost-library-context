# #1726 - Better explanation of tcp_stream timeouts and websocket interaction [Open]

> Username: reddwarf69  
> Created at: 2019-10-08 11:29:02 UTC  
> Updated at: 2019-10-08 14:35:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1726  

https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_websocket/timeouts.html says  
  
`The timeout features of the TCP or basic stream should not be used when working with a websocket stream.`  
  
and  
  
`The timeouts on the websocket stream are incompatible with the timeouts used in the tcp_stream. When constructing a websocket stream from a tcp stream that has timeouts enabled, the timeout should be disabled first before constructing the websocket stream, as shown.`  
  
I wanted to have a timeout for the "connection" of a protocol over websocket, with the timeout covering all of TCP, SSL, Websocket and over-Websocket.  
  
So I was planning to use the tcp_stream timeout starting with the TCP socket and only stopped after the websocket handshake.  
  
I was planning to disable the WS handshake_timeout, but leaving the WS idle_timeout. But the documentation seems to say I can't. Can the restriction be relaxed?  
I am not really sure why it's there. The WS stream needs to be able to work even if the underlying streams report an error and AFAIK an error::timeout is just another error.  
Maybe the documentation is just trying to say "your handshake may timeout earlier than expected if there is also a tcp_stream timeout"?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-08 12:24:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1726#issuecomment-539487620  

> I am not really sure why it's there.   
  
The restriction exists because the interaction between having two separate timeouts is undefined. It has not been tested, and I can't be sure that the errors will be delivered in the right order. It might leak. It could break invariants or cause asserts.  
  
Why do you want to disable the websocket handshake timeout?

---

## Comment 2

> Username: reddwarf69  
> Created at: 2019-10-08 13:25:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1726#issuecomment-539512267  

It's not that I want to disable the handshake timeout, but since I was planning to have a single tcp_stream timeout covering a time range from TCP connection to over-websocket protocol I didn't see a need for it. I don't have a specific timeout for any other intermediate operation, like the SSL handshake, so it made sense not to have it for the WebSocket handshake either.  
Looking into it better... the handshake_timeout also covers the close handshake, so I do actually need it enabled.  
  
But if I got it right I guess as long as the interaction between having two separate timeouts is undefined I'm going to need my own timeout in there. My over-websocket "connection" is more of an authentication. My server does  
  
TCP accept -> SSL handshake -> Websocket handshake -> App authentication -> Reply/Requests handling.  
  
With the current Beast timeouts support I can  
  
TCP accept -> start tcp_stream timeout -> SSL handshake -> stop tcp_stream timeout -> Websocket handshake (with ws::stream `handshake_timeout`) -> App authentication (with ws::stream `idle_timeout`).  
  
But as long as the client keeps sending WS pings the connection will be kept open. So a malicious client could force my server to keep lots of WS connections open indefinitely without having to know the app credentials. Not being able to use the tcp_stream timeout for my "App authentication" I would need to use my own timeout.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-08 13:31:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1726#issuecomment-539514453  

> ...a malicious client could force my server to keep lots of WS connections open indefinitely without having to know the app credentials  
  
The credentials are submitted as a websocket message? Hmm... I agree that this is a problem. Perhaps the control callback could be improved to get more information about events such as timeouts, or even allow a return value to control what happens on a timeout.

---

## Comment 4

> Username: reddwarf69  
> Created at: 2019-10-08 13:49:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1726#issuecomment-539522938  

> > ...a malicious client could force my server to keep lots of WS connections open indefinitely without having to know the app credentials  
>   
> The credentials are submitted as a websocket message?  
  
Yes.  
  
> Hmm... I agree that this is a problem. Perhaps the control callback could be improved to get more information about events such as timeouts, or even allow a return value to control what happens on a timeout.  
  
For my specific need I was hoping to be able to do  
  
TCP accept -> start tcp_stream timeout -> SSL handshake -> Websocket handshake (also with ws::stream handshake_timeout) -> App authentication (also with ws::stream idle_timeout) -> stop tcp_stream timeout  
  
But no idea how much work it would be to make this "correct" defined behaviour. And other people may have needs more complex than mine, so your idea may well be the best path forward, I don't know.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-10-08 13:53:28 UTC  
> Updated at: 2019-10-08 13:53:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1726#issuecomment-539524540  

That's not going to work. I would suggest instead that you simply use your own timer, just for the part where you expect the app authentication. And call `async_close` if that timer expires. This is in addition to the built-in websocket timeouts.

---

## Comment 6

> Username: reddwarf69  
> Created at: 2019-10-08 14:32:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1726#issuecomment-539542529  

OK, thanks.  
  
The `When constructing a websocket stream from a tcp stream that has timeouts enabled, the timeout should be disabled first before constructing the websocket stream, as shown.` is also literal? Can't I  
- Construct a websocket::stream<ssl_stream<tcp_stream>>;  
- Set tcp_stream timeout  
- ssl_stream.async_handshake  
- Stop tcp stream timeout  
- websocket::stream async_accept  
?  
  
Do I need to delay the websocket stream creation until after the ssl_stream.async_handshake completion handler is called?  
  
I didn't really look at the implementation, but I was surprised when I read it.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-10-08 14:34:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1726#issuecomment-539543283  

It should read _"...the timeout should be disabled first before initiating any asynchronous operations on the websocket stream, ..."_
