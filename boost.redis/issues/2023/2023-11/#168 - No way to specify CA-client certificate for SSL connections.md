# #168 - No way to specify CA/client certificate for SSL connections. [Closed]

> Username: grapland0  
> Created at: 2023-11-29 00:30:35 UTC  
> Updated at: 2025-09-08 20:33:58 UTC  
> Closed at: 2025-09-08 20:33:58 UTC  
> Url: https://github.com/boostorg/redis/issues/168  

It always constuct a system-default ssl context which uses system common CA set and no client certificate (for mutual authentation at server side).

---

## Comment 1

> Username: criatura2  
> Created at: 2023-11-30 09:53:44 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1833432614  

You can pass the context method to the connection constructor: https://github.com/boostorg/redis/blob/7caea928affde85146b738eb456d4772a410f721/include/boost/redis/connection.hpp#L346

---

## Comment 2

> Username: grapland0  
> Created at: 2023-11-30 15:19:51 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1833985064  

It is just the method. Can I pass the whole context in? I need to specify CA certificate, as well as client cert/key for mutual authentication.

---

## Comment 3

> Username: criatura2  
> Created at: 2023-11-30 16:12:10 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1834080549  

At the moment you can't pass the context. I agree that passing the whole context would have been more flexible, so I might change that in a future release. But I fail to see why you can't first pass the method and then set whatever you have to set. There is only one non-trivial constructor in ssl::context, which is the one you will be using anyway https://www.boost.org/doc/libs/1_83_0/doc/html/boost_asio/reference/ssl__context/context.html

---

## Comment 4

> Username: grapland0  
> Created at: 2023-12-01 01:01:15 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1835222250  

Is there any API I can use to directly access the SSL context of current connection from boost::redis?  
  
The `get_ssl_context` doesn't exist in executor-erased `connection` (https://github.com/boostorg/redis/blob/7caea928affde85146b738eb456d4772a410f721/include/boost/redis/connection.hpp#L337C7-L337C17)

---

## Comment 5

> Username: mzimbres  
> Created at: 2023-12-01 07:49:19 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1835630505  

Yeah it is missing. I will add one.

---

## Comment 6

> Username: grapland0  
> Created at: 2023-12-01 09:00:06 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1835718847  

I think just adding a constructor with ssl context passed in is sufficient, like Beast's secure websocket. Letting end users play with existing ssl context may interfere with ongoing async ops (or you'll need to document when they can/cannot make change safely)

---

## Comment 7

> Username: mzimbres  
> Created at: 2023-12-11 21:08:09 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1850890059  

Did not have the time to look at this yet. Perhaps in the forthcoming weekends.

---

## Comment 8

> Username: mzimbres  
> Created at: 2023-12-30 21:26:59 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1872610185  

@grapland0 It was not my intention to close the ticket, so please reopen if necessary. The PR adds the getters you were missing to the ssl-context. I am still unsure whether and how I will allow passing a custom ssl context to the connection. This is what Boost.MySql does (from a conversation with @anarthal)  
  
> The way mysql solves it (on the newer any_connection)  
>  
> If you want a custom SSL context, you pass a referene  
>  
> If you don't need a custom context but need TLS, you pass nullptr, and you receive a singleton reference with appropriate values  
>  
> If you don't need a context because you're not using TLS, you pass nullptr and nothing gets ever created  
>  
> If you're using a connection pool, SSL contexts are passed by value. A single context object is shared between all connections  
>  
> SSL contexts are unique pointers to openssl SSL_CTX objects. It's not documented how heavyweight they are, but they have an internal refcount mechanism, so they introduce some overhead, at least.  
>   
> I understand that using custom TLS connections without using a connection pool is something advanced and not too frequent

---

## Comment 9

> Username: anarthal  
> Created at: 2023-12-30 21:33:37 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1872611072  

Please note that it is UB to set properties of SSL contexts once a ssl::stream is created from them. If `connection` creates internally a `ssl::stream` when constructed, the `ssl::context` must not be modified after construction.

---

## Comment 10

> Username: mzimbres  
> Created at: 2024-01-03 16:21:16 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-1875630098  

Fix is merged now. Please have a look.

---

## Comment 11

> Username: anarthal  
> Created at: 2025-09-08 20:33:58 UTC  
> Url: https://github.com/boostorg/redis/issues/168#issuecomment-3267904213  

This is fixed as of today so I'll be closing this ticket.
