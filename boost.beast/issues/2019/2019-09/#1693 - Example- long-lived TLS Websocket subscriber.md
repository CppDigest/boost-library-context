# #1693 - Example: long-lived TLS Websocket subscriber [Closed]

> Username: kdowney-lot49  
> Created at: 2019-09-02 20:36:05 UTC  
> Updated at: 2019-10-04 22:16:09 UTC  
> Closed at: 2019-10-04 22:16:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1693  

I am currently using IXWebSocket and experimenting with websocketpp as a replacement for my C++ Websocket clients, but ideally I'd like to use Beast! The client examples are clear but am looking for something more advanced:  
  
* issue a subscription message and then receive a very long-lived stream of updates; the current TLS example just writes a message, reads a message, and exits  
* reconnect whenever a connection drops on server side, ideally policy-based (e.g. an exponential back-off and retry policy)  
  
Here's what the simplified client code wrapper I wrote for websocketpp looks like, which I'd want to rewrite to use Beast instead:  
  
https://github.com/cloudwall/libcrypto-mktdata/blob/master/include/cloudwall/crypto-mktdata/websocket_client.h  
https://github.com/cloudwall/libcrypto-mktdata/blob/master/src/websocket_client.cpp  
https://github.com/cloudwall/libcrypto-mktdata/blob/master/src/print_client_tls.cpp  
  
Beast: 1.69 (requested 1.71 from Conan repository)  
OS: Linux - Debian "Stretch"  
Compiler: gcc 9.2.0

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-02 20:38:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1693#issuecomment-527239536  

For the pub/sub have a look at the `channel` implementation in this other project I am working on which uses Beast websockets: https://github.com/vinniefalco/BeastLounge

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-09-02 21:25:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1693#issuecomment-527245172  

> reconnect whenever a connection drops on server side, ideally policy-based (e.g. an exponential back-off and retry policy)  
  
Boost.Beast doesn't manage sockets or connections, this is handled in Boost.Asio, so your solution is going to rely on Asio interfaces exclusively. There's no secret sauce here, you can implement your reconnection logic any way that you like.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-10-02 21:57:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1693#issuecomment-537699242  

This issue has been open for a while with no activity, has it been resolved?
