# #2917 - Websocket example [Closed]

> Username: Dimitrius-dev  
> Created at: 2024-08-08 11:03:42 UTC  
> Updated at: 2024-11-25 13:43:51 UTC  
> Closed at: 2024-11-25 13:43:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2917  

Hello,  
Boost=1.84.0, BOOST_BEAST_VERSION=351  
I am using boost/beast websocket TLS async client example [link](https://github.com/boostorg/beast/blob/develop/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp). I noticed my connection disconnects sometimes with `stream truncated` and `eof`. What do these errors mean? I checked my internet connection with wireshark and it's fine.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-08-08 11:19:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2275574894  

Do they happen at the end of stream? [error::stream_truncated](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/ssl_tls_shutdown.html#beast.using_io.ssl_tls_shutdown.error_stream_truncated)

---

## Comment 2

> Username: Dimitrius-dev  
> Created at: 2024-08-08 11:50:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2275628117  

No, i get these errors without calling any shutdown or disconnect function. And it shows when use client for high load traffic (high frequency).

---

## Comment 3

> Username: ashtum  
> Created at: 2024-08-08 11:54:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2275635860  

Are you sure it’s not the server that dropped the connection? Does this happen with just one specific server?

---

## Comment 4

> Username: Dimitrius-dev  
> Created at: 2024-08-26 10:55:08 UTC  
> Updated at: 2024-08-26 10:55:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2309924819  

Can connection issues occur if multiply connections use load_certificates function?

---

## Comment 5

> Username: ashtum  
> Created at: 2024-08-26 11:15:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2309958608  

Do you mean `load_root_certificates`? it calls [ssl::context::add_certificate_authority](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/ssl__context/add_certificate_authority.html) which is not thread safe.

---

## Comment 6

> Username: Dimitrius-dev  
> Created at: 2024-08-26 11:40:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2310000505  

I made n threads. Every thread has its own asio context, and interact with the same internet resource

---

## Comment 7

> Username: ashtum  
> Created at: 2024-08-26 12:39:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2310115809  

Do they have their own `ssl::context` too?

---

## Comment 8

> Username: Dimitrius-dev  
> Created at: 2024-08-26 13:34:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2310232215  

yes

---

## Comment 9

> Username: ashtum  
> Created at: 2024-08-26 13:39:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2917#issuecomment-2310242856  

Have you tried reducing your application to a single connection and a single thread to see if the issue still exists? If possible, test one of the existing examples.
