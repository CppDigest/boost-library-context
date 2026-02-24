# #824 - shutdown: stream truncated [Closed]

> Username: no-realm  
> Created at: 2017-10-21 15:49:54 UTC  
> Updated at: 2018-02-21 00:08:21 UTC  
> Closed at: 2017-10-21 16:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/824  

Hi,  
  
when I am using an ssl stream, like in your http client [example](http://www.boost.org/doc/libs/develop/libs/beast/example/http/client/sync-ssl/http_client_sync_ssl.cpp), I am getting an error when shutting down the stream:  
```  
shutdown: stream truncated  
```  
I can just ignore it by checking against that specific error `ec == boost::asio::ssl::error::stream_truncated`, but I wanted to know if this is 'normal' since in the example you only check for `operation_aborted`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-21 15:57:23 UTC  
> Updated at: 2017-10-21 15:57:50 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-338412225  

It is technically an error, but unfortunately many HTTP servers and HTTP clients are misbehaved, see: https://github.com/boostorg/beast/issues/38  
  
You might need to check for it specifically and just ignore it.

---

## Comment 2

> Username: no-realm  
> Created at: 2017-10-21 16:01:34 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-338412636  

Oh ok. Then I will just ignore it when shutting down the stream.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-21 16:02:39 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-338412703  

Just a note, usually it is better to let the server perform the close. As a client you can just wait for the server to close the connection (set a timeout in case this never happens). This will result in the server receiving the TIME_WAIT state rather than the client, which is preferable: http://www.serverframework.com/asynchronousevents/2011/01/time-wait-and-its-design-implications-for-protocols-and-scalable-servers.html

---

## Comment 4

> Username: no-realm  
> Created at: 2017-10-21 16:13:36 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-338413527  

What do you mean with wait?  
Am I supposed to just not shutdown?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-21 16:24:25 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-338414346  

You should read the article

---

## Comment 6

> Username: pal666  
> Created at: 2018-02-20 19:48:43 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-367097363  

you are contradicting your article, it says client should close socket, server should wait for client to close

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-02-20 21:33:48 UTC  
> Updated at: 2018-02-20 21:35:28 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-367127557  

>you are contradicting your article, it says client should close socket, server should wait for client to close  
  
The article states clearly:  
  
_"Inbound connections are less affected by TIME_WAIT. Whilst the a connection that is actively closed by a server goes into TIME_WAIT exactly as a client connection does the local port that the server is listening on is not prevented from being part of a new inbound connection."_  
  
Thus, the server should initiate the close. Also, the WebSocket protocol RFC states that the server should initiate the close:  
  
The article also states:  
  
_"A TCP peer initiates an "active close" if it is the first peer to call Close() on the connection. In many protocols and client/server designs this is the client. In HTTP and FTP servers this is often the server. "_  
  
There is an overwhelming preponderance of evidence which states that the server should 1. initiate the TCP close process, and 2. prefer to be left with the TIME_WAIT state.

---

## Comment 8

> Username: pal666  
> Created at: 2018-02-20 21:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-367128928  

the article clearly states   
  
> For a server that does establish outbound connections as well as accepting inbound connections then the golden rule is to always ensure that if a TIME_WAIT needs to occur that it ends up on the other peer and not the server. The best way to do this is to never initiate an active close from the server, no matter what the reason. If your peer times out, abort the connection with an RST rather than closing it. If your peer sends invalid data, abort the connection, etc.  
  
all servers do establish outbound connections

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-02-20 21:41:59 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-367129896  

>all servers do establish outbound connections  
  
What? No... an outbound connection is established by calling `boost::asio::connect` or `boost::asio::async_connect`. Beast example servers do not make outgoing connections, they accept incoming connections.  This is true for most web servers. What outgoing connections do you think Apache makes?

---

## Comment 10

> Username: pal666  
> Created at: 2018-02-20 21:55:46 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-367133807  

in this context outbound connection is established by calling connect(3) and "server"  means host, not process, since ports are global  
any host establishes outbound connections  
but since you've asked about apache, how do you think mod_proxy works?

---

## Comment 11

> Username: nbougalis  
> Created at: 2018-02-21 00:07:51 UTC  
> Updated at: 2018-02-21 00:08:21 UTC  
> Url: https://github.com/boostorg/beast/issues/824#issuecomment-367166194  

>all servers do establish outbound connections  
  
Typically servers `listen` and `accept` which means they deal with _inbound_ connections. It's true that some servers also make _outbound_ connections (e.g. IRC servers). The problem here, seems to me, to be one of terminology.   
  
If that's not accurate, then I'm not sure exactly what you're driving to @pal666.
