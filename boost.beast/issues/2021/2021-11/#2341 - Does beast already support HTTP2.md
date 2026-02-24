# #2341 - Does beast already support HTTP2? [Closed]

> Username: kqbi  
> Created at: 2021-11-12 06:18:45 UTC  
> Updated at: 2021-11-29 03:49:21 UTC  
> Closed at: 2021-11-29 03:49:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2341  

Does beast already support HTTP2? demo?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-11-12 07:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2341#issuecomment-966874981  

Nope. And no plans to do so.

---

## Comment 2

> Username: brjoha  
> Created at: 2021-11-20 22:13:48 UTC  
> Updated at: 2021-11-20 22:14:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2341#issuecomment-974718964  

I'm using it in a project that supports both HTTP/1.1 and HTTP/2.  
  
When HTTP/2 is negotiated, the nghttp2 lib is used for the session layer (over asio) but Beast request and response types are still used above that.  I expect to follow the same approach for HTTP/3.
