# #2023 - Missing TCP connection teardown in Websocket when wrapped in net::ssl::stream [Closed]

> Username: TheStormN  
> Created at: 2020-07-18 15:24:55 UTC  
> Updated at: 2020-08-29 09:39:50 UTC  
> Closed at: 2020-08-29 09:39:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2023  

### Version of Beast  
  
Boost 1.73.0  
BOOST_BEAST_VERSION 290  
  
### Steps necessary to reproduce the problem  
When using WWS client + server I noticed that only the net::ssl::stream is shutdown properly by `beast` but the TCP socket is left behind. When using the same scenario without SSL, `beast` takes care of shutting down the TCP socket properly.  
  
I'm not sure if this is an issue or it is intended to work like that, so I've decided to post here. :)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-18 15:33:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2023#issuecomment-660499506  

Working as designed. Usually the application will destroy the socket object, and this will close the connection.

---

## Comment 2

> Username: TheStormN  
> Created at: 2020-07-18 16:13:30 UTC  
> Updated at: 2020-07-18 21:51:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2023#issuecomment-660504689  

@vinniefalco Directly calling `close()/closesocket()` on socket without proper shutdown have different behavior on different platforms.  
For example on Windows it will try to do proper shutdown by sending FIN/FIN-ACK/ACK, etc, while on Linux it will directly destroy the socket with RST.  
  
In the Windows case ASIO is using socket linger to avoid blocking on destruction, so basically the OS is spawning a new thread that will continue to manage the socket until it is properly shutdown(which is an overhead). When we do proper shutdown this thread is avoided.  
  
What I'm trying to say is that if you don't care about the socket shutdown, you can pretty much remove the TCP teardown functionality and leave the library user to do it, or you can do it every time when you have a supported stream/nested supported streams.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-07-18 18:30:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2023#issuecomment-660522664  

I was not aware of this, it will need some research. @madmongo1

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-07-19 10:28:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2023#issuecomment-660622506  

What behaviour do we want? Tear down the SSL connection but leave the TCP connection up, or tear down both?

---

## Comment 5

> Username: TheStormN  
> Created at: 2020-07-19 10:33:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2023#issuecomment-660623035  

I would say - tear down both.  
  
Currently if it is WS socket, the TCP connection is tear down. If it is WSS socket, only the SSL connection is tear down, which is half of the work, after the SSL is teared down, the TCP layer should be also teared down.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-08-19 07:31:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2023#issuecomment-675904281  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-08-19 13:14:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2023#issuecomment-676336765  

ping - there is a fix in the works for this.
