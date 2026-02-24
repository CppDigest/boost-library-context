# #2993 - Boost Beast 1.86.0 on iOS 18.3.1 connection times out [Closed]

> Username: agingo  
> Created at: 2025-03-10 15:32:41 UTC  
> Updated at: 2025-03-13 15:47:30 UTC  
> Closed at: 2025-03-13 15:47:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2993  

We use Boost Beast (1.86.0) for WebSockets in an iOS application using a self-signed certificate. The Beast WebSocket client works fine on iOS 18.1 and every other OS but not iOS 18.3.1 and possibly versions before 18.3.1 also.  
  
Has anyone else ran into this issue and how did you resolve?  
  
Xcode 16.2

---

## Comment 1

> Username: ashtum  
> Created at: 2025-03-10 15:36:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2711008595  

Could you please elaborate on the problem? Do you get any OpenSSL errors?

---

## Comment 2

> Username: ashtum  
> Created at: 2025-03-10 15:40:57 UTC  
> Updated at: 2025-03-10 15:44:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2711023665  

This might be related: https://developer.apple.com/forums/thread/764673  
You might be able to create a CA certificate and make a self-issued certificate instead of a self-signed one.  
[Self-Signed and Self-Issued Certificates](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/ssl_tls_certificate.html#beast.using_io.ssl_tls_certificate.self_signed_and_self_issued_cert)  
[A Self-Issued Certificate Example](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/ssl_tls_certificate.html#beast.using_io.ssl_tls_certificate.a_self_issued_certificate_exampl)

---

## Comment 3

> Username: agingo  
> Created at: 2025-03-10 15:49:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2711048973  

> This might be related: https://developer.apple.com/forums/thread/764673 You might be able to create a CA certificate and make a self-issued certificate instead of a self-signed one. [Self-Signed and Self-Issued Certificates](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/ssl_tls_certificate.html#beast.using_io.ssl_tls_certificate.self_signed_and_self_issued_cert) [A Self-Issued Certificate Example](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/ssl_tls_certificate.html#beast.using_io.ssl_tls_certificate.a_self_issued_certificate_exampl)  
  
That was the next thing we were going to try... I'd be surprised if they started requiring issued certificates, but I probably shouldn't be given some of the recent threats.

---

## Comment 4

> Username: agingo  
> Created at: 2025-03-10 15:49:27 UTC  
> Updated at: 2025-03-10 15:50:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2711050130  

> Could you please elaborate on the problem? Do you get any OpenSSL errors?  
  
No issues with OpenSSL and we also tried the latest commit but it didn't resolve the issue.

---

## Comment 5

> Username: agingo  
> Created at: 2025-03-12 17:29:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718616204  

A little more info for you guys. The issue is specifically related to secure async websocket calls on iOS 18.3.1. The same iOS framework wrapped C++ library works fine on iOS 18.1.0. Secure Synchronous Websocket calls work fine on any version of iOS.  
  
Any ideas on what's causing async websocket connections to timeout only on iOS 18.3.1?

---

## Comment 6

> Username: ashtum  
> Created at: 2025-03-12 17:37:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718635687  

The issue might not be related to WebSocket. Could you try a simple [asio::ssl::stream](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/ssl__stream.html) client example and see if it works properly?

---

## Comment 7

> Username: agingo  
> Created at: 2025-03-12 17:39:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718639290  

> This might be related: https://developer.apple.com/forums/thread/764673 You might be able to create a CA certificate and make a self-issued certificate instead of a self-signed one. [Self-Signed and Self-Issued Certificates](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/ssl_tls_certificate.html#beast.using_io.ssl_tls_certificate.self_signed_and_self_issued_cert) [A Self-Issued Certificate Example](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/ssl_tls_certificate.html#beast.using_io.ssl_tls_certificate.a_self_issued_certificate_exampl)  
  
We tried official SSL certs and still had the problem.

---

## Comment 8

> Username: ashtum  
> Created at: 2025-03-12 17:44:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718653003  

Could you please elaborate on what doesn't work and what error messages you receive?    
Does the TLS handshake on the underlying socket of the WebSocket stream (SSL stream) complete successfully?

---

## Comment 9

> Username: agingo  
> Created at: 2025-03-12 18:54:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718822844  

> Could you please elaborate on what doesn't work and what error messages you receive? Does the TLS handshake on the underlying socket of the WebSocket stream (SSL stream) complete successfully?  
  
The connection simply times out without an error after we call async_resolve().  
  
Specifically, when calling  **resolver_.async_resolve(host, port, beast::bind_front_handler(&session::on_resolve, shared_from_this()));** the on_resolve() is never called... it simply times out.   
  
We are using a valid SSL certificate and DNS works.

---

## Comment 10

> Username: ashtum  
> Created at: 2025-03-12 19:03:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718844965  

> The connection simply times out without an error after we call async_resolve().  
>   
> Specifically, when calling **resolver_.async_resolve(host, port, beast::bind_front_handler(&session::on_resolve, shared_from_this()));** the on_resolve() is never called... it simply times out.  
>   
> We are using a valid SSL certificate and DNS works.  
  
So it never even reaches the TCP connection or the TLS handshake?  
Could you replace just `async_resolve` with the sync version and see if the rest of the code works or not?

---

## Comment 11

> Username: agingo  
> Created at: 2025-03-12 19:48:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718935669  

We did and it works fine on iOS 18.3.1. However, I’ve read online that synchronous works and drops after a while.

---

## Comment 12

> Username: agingo  
> Created at: 2025-03-12 19:53:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718945870  

Libdatachannels secure sync web sockets library works on iOS 18.3.1

---

## Comment 13

> Username: ashtum  
> Created at: 2025-03-12 20:01:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2718966616  

Based on your description, the problem is not related to the WebSocket stream but rather to the [ip::tcp::resolver](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/ip__tcp/resolver.html) object.  
  
It would be helpful to raise an issue in the Asio repository: https://github.com/chriskohlhoff/asio/issues  
  
  
> We did and it works fine on iOS 18.3.1. However, I’ve read online that synchronous works and drops after a while.  
  
I suggest replacing `async_resolve` with `resolve` to see if the rest of the asynchronous code still works. Have you tried that?

---

## Comment 14

> Username: agingo  
> Created at: 2025-03-13 15:47:05 UTC  
> Updated at: 2025-03-13 15:47:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2993#issuecomment-2721726158  

The recently pushed commits for Boost Beast solved our problem with iOS 18.3.1 and secure async websockets. Thank you.
