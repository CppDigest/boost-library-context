# #1217 - How to connect to URI with parameters? [Closed]

> Username: tensor5375  
> Created at: 2018-08-07 07:28:15 UTC  
> Updated at: 2018-08-10 02:16:47 UTC  
> Closed at: 2018-08-10 02:16:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1217  

[Environment]  
Windows 10 64bit  
Visual C++ 2017  
Boost C++ Library 1.67  
  
[Problem]  
I'm trying to connect to WebSocket server that has a URL like below.  
  
ws://a.live2.nicovideo.jp:2805/unama/wsapi/v1/watch/17394521080445?audience_token=17394521080445_84484127_1533712910_4e130746d630b25abfd4441c0e3f58e04a40bd5a  
  
How can I connect to this url? Must I set header parameters manually?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-07 10:16:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1217#issuecomment-411007810  

First, perform a DNS lookup on `"a.live2.nicovideo.jp"`. Then, connect to port 2805 on the IP address returned by the DNS lookup. Once you have a connection, you can handshake using  
```  
ws.handshake(hostname, "/unama/wsapi/v1/watch/17394521080445?audience_token=17394521080445_84484127_1533712910_4e130746d630b25abfd4441c0e3f58e04a40bd5a");  
```  
Where `ws` is your connected `websocket::stream` object.

---

## Comment 2

> Username: tensor5375  
> Created at: 2018-08-07 11:00:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1217#issuecomment-411018505  

Thanks, vinniefalco.　I'll try it.

---

## Comment 3

> Username: tensor5375  
> Created at: 2018-08-07 22:50:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1217#issuecomment-411227659  

Handshake seems succeeded.   
I'll try to check further behaviors.

---

## Comment 4

> Username: tensor5375  
> Created at: 2018-08-10 02:16:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1217#issuecomment-411954832  

I succeeded to communicate with server by modifying the request header for websocket protocol.  
The issue below was very useful to understand.  
https://github.com/boostorg/beast/issues/991
