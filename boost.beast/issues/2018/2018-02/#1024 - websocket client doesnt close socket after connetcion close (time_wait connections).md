# #1024 - websocket client doesnt close socket after connetcion close (time_wait connections) [Closed]

> Username: krsvital  
> Created at: 2018-02-16 05:37:40 UTC  
> Updated at: 2021-07-28 04:04:24 UTC  
> Closed at: 2018-02-21 16:33:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1024  

boost beast version 1.67  
  
after compilling this async clien  
  
http://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async/websocket_client_async.cpp  
  
and i try to connect to   
  
http://www.boost.org/doc/libs/master/libs/beast/example/websocket/server/async/websocket_server_async.cpp  
  
then after close session and after "netstat -na |  grep [websocket port]" command, i see time_wait connections like this  
  
tcp        0      0 127.0.0.1:6006          0.0.0.0:*               LISTEN  
tcp        0      0 127.0.0.1:6006          127.0.0.1:54280         TIME_WAIT  
tcp        0      0 127.0.0.1:6006          127.0.0.1:54282         TIME_WAIT  
tcp        0      0 127.0.0.1:6006          127.0.0.1:54279         TIME_WAIT  
tcp        0      0 127.0.0.1:6006          127.0.0.1:54284         TIME_WAIT  
  
but if i run websocket client in chrome browser and close the page, time_wait is absent...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-17 14:22:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366444606  

>boost beast version 1.67  
  
There is no Boost 1.67 yet, did you mean 1.66? Which version of Beast are you using?

---

## Comment 2

> Username: krsvital  
> Created at: 2018-02-18 15:17:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366523162  

I download the last version of boost from github in which incude the beast module

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-19 21:45:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366810021  

TIME_WAIT on the server is normal. Your netstat shows "Local address" with port 6006, which I am assuming is the server port. When a TCP/IP connection is shut down, the side initiating the close procedure is left in the TIME_WAIT state. It is preferable for the server to be left with this state than the client (see http://www.serverframework.com/asynchronousevents/2011/01/time-wait-and-its-design-implications-for-protocols-and-scalable-servers.html).  
  
I don't know why Chrome doesn't leave the server with a TIME_WAIT but it could be that it is caching the open connection.

---

## Comment 4

> Username: krsvital  
> Created at: 2018-02-20 08:23:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366900224  

if client close connection TIME_WAIT can not be a normal state, becouse server take a "close frame" and close socket by self side, but client side socket doesnt close and client websocket program shutdown without close socket, so TIME_WAIT connections appears in system  
  
when async_close function are finished (websocket_client_async.cpp) client shutdown without close socket by itself

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-02-20 10:55:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366940851  

Beast performs TCP shutdown through the "teardown" mechanism. The server sends a FIN, and then the client responds with a FIN. This should leave the server with the TIME_WAIT state. That code is here:  
  
https://github.com/boostorg/beast/blob/de8d0b68435b57024d1263af6d4b335a805ac333/include/boost/beast/websocket/impl/teardown.ipp#L105  
and  
https://github.com/boostorg/beast/blob/de8d0b68435b57024d1263af6d4b335a805ac333/include/boost/beast/websocket/impl/teardown.ipp#L131  
  
However, it looks like there could be a bug in the asynchronous version. Could you please repeat the test using the synchronous client and server and let me know if it operates correctly?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-02-20 11:36:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366950621  

I have implemented a possible fix to the asynchronous teardown code, please try the **v157** branch located in my repository:  
https://github.com/vinniefalco/beast/commits/v157

---

## Comment 7

> Username: krsvital  
> Created at: 2018-02-20 11:37:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366950859  

i repeat the test with standart sync client ( http://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/sync/websocket_client_sync.cpp )  
and with other async websocket server ( rippled ) and with standart ( http://www.boost.org/doc/libs/develop/libs/beast/example/websocket/server/sync/websocket_server_sync.cpp ) server and allways i see TIME_WAIT connections...

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-02-20 11:38:51 UTC  
> Updated at: 2018-02-20 11:42:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366951156  

Remember that TIME_WAIT on the server is normal. I'm fairly certain that Beast has the correct behavior here, per https://tools.ietf.org/html/rfc6455#section-5.5.1:  
  
_" After both sending and receiving a Close message, an endpoint considers the WebSocket connection closed and MUST close the underlying TCP connection.  The server MUST close the underlying TCP connection immediately; the client SHOULD wait for the server to close the connection but MAY close the connection at any time after sending and receiving a Close message, e.g., if it has not received a TCP Close from the server in a reasonable time period."_  
  
The rfc prescribes that the server send the FIN first. And this is exactly what Beast does:  
https://github.com/vinniefalco/beast/blob/v157/include/boost/beast/websocket/impl/teardown.ipp#L157

---

## Comment 9

> Username: krsvital  
> Created at: 2018-02-20 11:57:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366955299  

my problem was solved by set sysctl  
  
net.ipv4.tcp_tw_reuse = 1  
net.ipv4.tcp_tw_recycle = 1 ### this value was "0"  
  
thanks for taking the time to my question!

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-02-20 12:07:36 UTC  
> Updated at: 2018-02-20 12:07:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-366957602  

>net.ipv4.tcp_tw_recycle = 1 ### this value was "0"  
  
See https://vincent.bernat.im/en/blog/2014-tcp-time-wait-state-linux

---

## Comment 11

> Username: toddwong  
> Created at: 2021-07-28 04:03:45 UTC  
> Updated at: 2021-07-28 04:04:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1024#issuecomment-887991769  

> TIME_WAIT on the server is normal. Your netstat shows "Local address" with port 6006, which I am assuming is the server port. When a TCP/IP connection is shut down, the side initiating the close procedure is left in the TIME_WAIT state. It is preferable for the server to be left with this state than the client (see http://www.serverframework.com/asynchronousevents/2011/01/time-wait-and-its-design-implications-for-protocols-and-scalable-servers.html).  
>   
> I don't know why Chrome doesn't leave the server with a TIME_WAIT but it could be that it is caching the open connection.  
  
but in (http://www.serverframework.com/asynchronousevents/2011/01/time-wait-and-its-design-implications-for-protocols-and-scalable-servers.html)  
  
"For a server that does establish outbound connections as well as accepting inbound connections then the golden rule is to always ensure that if a TIME_WAIT needs to occur that it ends up on the other peer and not the server. The best way to do this is to never initiate an active close from the server, no matter what the reason"  
  
Which do against the recommended closing precedure in WebSocket RFC?
