# #1547 - How to handle HTTP/2 upgrade request? [Closed]

> Username: brjoha  
> Created at: 2019-03-28 12:19:39 UTC  
> Updated at: 2019-04-19 12:58:37 UTC  
> Closed at: 2019-04-19 12:58:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1547  

I need to build a server that supports both HTTP/1.1 and HTTP/2.  My hope is to leverage Beast and nghttp2 for this purpose, where Beast is HTTP/1.1 only and nghttp2 is HTTP/2 only, and use the Beast message classes in either case.  
  
Where I need some guidance is on how to best handle upgrade to HTTP/2.  My understanding is that upgrade is negotiated with the HTTP upgrade header on an unsecured connection, and TLS ALPN on a secured connection.  Beast documentation on WebSocket upgrade here...  
  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html  
  
...would seem to suggest an unsecured HTTP/2 upgrade request could be handled similarly.  What about the secured upgrade case?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-28 13:23:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-477592833  

> What about the secured upgrade case?  
  
Hmm... my understanding is that TLS ALPN puts the upgrade request into the ClientHello, which is the first "message" sent on a TLS connection. When using Boost.Asio it is OpenSSL which processes this message (via the `asio::ssl::stream`). This happens in the function `ssl::stream::async_handshake`. By the time Asio or Beast get back control from OpenSSL, the message is already processed.  
  
Therefore, you would have to either inspect the ClientHello yourself to see if an upgrade request is tucked away inside, or use some OpenSSL interface which lets you get at it (I am unfamiliar with OpenSSL, so this interface may or may not actually exist).  
  
The beast `detect_ssl` algorithm does limited parsing of this ClientHello message so you could use that code as a starting point if you want to parse it yourself:  
https://github.com/boostorg/beast/blob/5154233350d13a08d70f0a3a46c73bb1093225dd/include/boost/beast/core/detect_ssl.hpp#L96

---

## Comment 2

> Username: djarek  
> Created at: 2019-03-28 15:39:36 UTC  
> Updated at: 2019-03-28 15:41:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-477650234  

You should set up nginx as a reverse proxy that forwards requests to your Beast backend, something like this:  
```  
upstream https_backend {  
server 127.0.0.1:8080;  
  
keepalive 16;  
}  
  
server {  
listen 443 ssl http2;  
  
location /https/ {  
proxy_pass https://https_backend;  
proxy_http_version 1.1;  
}  
}  
```  
  
Note: I haven't tested this so I'm not sure if it works, might need some tweaking. Also, make sure you have OpenSSL 1.0.2 or higher and that the nginx build you use supports HTTP/2.  
  
In this configuration, nginx will automatically translate the HTTP/1.1 between your Beast server into and from HTTP/2.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-28 15:57:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-477657932  

**NOICE!!!**

---

## Comment 4

> Username: brjoha  
> Created at: 2019-04-11 22:10:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-482345408  

Dropping in a proxy is, unfortunately, not practical for my application.  We have the option to defer upgrade, and used fixed HTTP/1.1 or HTTP/2 on a given port for now.  However, it would be nice if Beast were to support an upgrade hooks in the secured case if possible.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-04-11 23:38:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-482373627  

Surely you can't expect Beast to do any OpenSSL heavy lifting...

---

## Comment 6

> Username: brjoha  
> Created at: 2019-04-12 03:36:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-482424891  

I can't argue with preserving the clean separation of session and transport, but the HTTP/2 working group thought otherwise for some reason.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-04-12 03:40:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-482425445  

Well you can still use Beast to parse the HTTP Upgrade request, but you would be responsible for extracting the serialized HTTP message from the TLS ClientHello record.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-04-19 01:32:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-484740110  

is this issue actionable?

---

## Comment 9

> Username: brjoha  
> Created at: 2019-04-19 12:58:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1547#issuecomment-484888047  

In the regular upgrade request case, we can follow the websocket example to handle HTTP/2.0.  It's just the SSL/ALPN upgrade request case that is problematic, but there's nothing actionable by Beast.  
  
nghttp2 provides client and server side examples of the ALPN negotiation, so I think we abstract that process and switch to Beast or nghttp2 accordingly...  
  
https://nghttp2.org/documentation/tutorial-client.html  
https://nghttp2.org/documentation/tutorial-server.html
