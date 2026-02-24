# #445 - HTTP: non-blocking stream algorithms [Closed]

> Username: vinniefalco  
> Created at: 2017-06-08 21:55:30 UTC  
> Updated at: 2018-12-21 15:48:58 UTC  
> Closed at: 2018-12-21 15:48:58 UTC  
> Url: https://github.com/boostorg/beast/issues/445  

We might set non blocking mode on a socket and then use the `async_read` with `null_buffers` to know when the socket is ready for reading, then use synchronous I/O. The advantage is we can do the actual non-blocking synchronous read using a stack based buffer. If the read fails because there is not enough input, we would have to then allocate to store the buffer for the next call but this could eliminate allocation in a lot of cases. This would radically change the performance profile of a server, as well as making it considerably easier to understand and debug.  
  
This should be attempted first as a server in test/ and then could be developed into a public example.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-09 00:42:41 UTC  
> Updated at: 2017-06-09 00:42:55 UTC  
> Url: https://github.com/boostorg/beast/issues/445#issuecomment-307264750  

Also applicable to WebSocket:  
  
It might be possible to get epoll style conservation of memory by reading frames with boost::asio::null_buffers. It would work like this, first read the frame header into a static streambuf. Then issue a call to async_read_some for the payload, but use boost::asio::null_buffers to defer the memory allocation. When that completes, now we know that data is available we can issue a call to read_some for read_size_helper(dynabuf, 65536) bytes in the dynabuf output area. The benefit here is a lower memory footprint. See:  
http://www.boost.org/doc/html/boost_asio/overview/core/reactor.html

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-11-28 00:58:22 UTC  
> Url: https://github.com/boostorg/beast/issues/445#issuecomment-442277528  

This is entirely doable for HTTP, and we can use `async_wait` instead of `null_buffers`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-21 15:48:58 UTC  
> Url: https://github.com/boostorg/beast/issues/445#issuecomment-449423346  

I'll note that this is implemented and can be turned on (currently affecting only HTTP operations):  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/detail/impl/read.hpp#L165  
  
I didn't really see a performance gain in fact it was a couple of percentage points worse, although this could be mitigated by the reduced memory consumption.
