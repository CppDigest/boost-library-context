# #158 - Explore epoll-style null_buffers [Closed]

> Username: vinniefalco  
> Created at: 2016-10-27 20:58:55 UTC  
> Updated at: 2017-06-09 00:42:30 UTC  
> Closed at: 2017-06-09 00:42:30 UTC  
> Url: https://github.com/boostorg/beast/issues/158  

It might be possible to get epoll style conservation of memory by reading frames with `boost::asio::null_buffers`. It would work like this, first read the frame header into a static streambuf. Then issue a call to `async_read_some` for the payload, but use `boost::asio::null_buffers` to defer the memory allocation. When that completes, now we know that data is available we can issue a call to `read_some` for `read_size_helper(dynabuf, 65536)` bytes in the dynabuf output area. The benefit here is a lower memory footprint. See:  
http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/overview/core/reactor.html

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-09 00:42:30 UTC  
> Url: https://github.com/boostorg/beast/issues/158#issuecomment-307264729  

This is a variation of https://github.com/vinniefalco/Beast/issues/445
