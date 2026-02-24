# #2699 - async-ssl: alert decrypt error [Closed]

> Username: definesun  
> Created at: 2023-06-27 03:14:59 UTC  
> Updated at: 2023-10-28 16:33:00 UTC  
> Closed at: 2023-10-28 16:33:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2699  

### Version of Beast  
boost_1_71_0  
  
hello, i use async-ssl in my project refer to http_server_async_ssl.cpp example, and https server user self-signed certificate,  bidirectional check between client and server, when config one thread, erverything is ok, but more than one threads, occasionally curl client return curl: (35) OpenSSL/3.1.1: error:02000086:rsa routines::last octet invalid, and server Handshake error with tlsv1 alert decrypt error. I don't know why, can you help me ?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-27 08:27:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2699#issuecomment-1609033748  

That does sound like an invalid certificate.

---

## Comment 2

> Username: definesun  
> Created at: 2023-06-27 09:32:55 UTC  
> Updated at: 2023-06-27 09:37:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2699#issuecomment-1609136097  

> That does sound like an invalid certificate.  
  
req one by one is ok， or https server fix one thread also is ok

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-06-28 04:22:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2699#issuecomment-1610685079  

I don't understand.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-08-19 05:15:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2699#issuecomment-1684823548  

@definesun Is this still open?
