# #1590 - beast::http_write() and EAGAIN [Closed]

> Username: brjoha  
> Created at: 2019-04-23 16:29:26 UTC  
> Updated at: 2019-04-23 20:35:54 UTC  
> Closed at: 2019-04-23 20:34:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1590  

We just upgraded to Boost 1.70.0 to pick up Beast support for timed async operations (nice addition).  
  
We're setting a timed beast::tcp_stream when calling beast::http::async_read(), but our write behavior is different.  We would prefer to call beast::http_write() and disconnect if the operation will block.  Does this method return the system_error equivalent of EAGAIN in this case?  
  
It appears we can set the non-blocking mode on an ASIO socket, and synchronous calls are supposed to fail with boost::asio::error::would_block if they cannot run immediately.  Will this propagate through the beast::http_write() method?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-23 17:44:03 UTC  
> Updated at: 2019-04-23 17:44:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1590#issuecomment-485904561  

I have no idea. I don't mix synchronous and asynchronous calls on the same I/O object, and I have not made much use of non-blocking mode. You would need to study the implementation of `http::write` to know for sure (and please let me know what you discover!)

---

## Comment 2

> Username: brjoha  
> Created at: 2019-04-23 20:34:01 UTC  
> Updated at: 2019-04-23 20:35:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1590#issuecomment-485963643  

Rigged up a server example to call http_write() in a loop on a socket I had previously set to non-blocking and it did congest and come back with EAGAIN.  
  
BTW, that rationale here is that if the peer (whether it be a client or server) is congested or otherwise incapacitated, then there's not much point in keeping the session up.
