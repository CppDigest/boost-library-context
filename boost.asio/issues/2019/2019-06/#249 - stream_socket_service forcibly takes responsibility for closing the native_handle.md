# #249 - stream_socket_service forcibly takes responsibility for closing the native_handle [Closed]

> Username: Glandos  
> Created at: 2019-06-03 15:35:58 UTC  
> Updated at: 2020-12-30 01:05:49 UTC  
> Closed at: 2020-12-30 01:05:48 UTC  
> Url: https://github.com/boostorg/asio/issues/249  

(Imported from https://svn.boost.org/trac10/ticket/12545)  
  
When using the `basic_stream_socket` ​[constructor](https://github.com/boostorg/asio/blob/da23b05953686041f23ac5749388c53182c507b8/include/boost/asio/basic_stream_socket.hpp#L214) with a `native_handle_type`, it seems that the service will call `close()` on it [​upon destruction](https://github.com/boostorg/asio/blob/ae04c26689116699d55af3976a6fc074831238be/include/boost/asio/detail/impl/reactive_socket_service_base.ipp#L92).  
  
I have no expectation on this behavior, but this should be stressed in the documentation. Using this constructor (or the `assign()` method) is very handy when dealing with third-parties, but those very same third-parties can have the same policy of auto-closing.  
  
This will lead to double closing, which is the best way for data corruption in a process that constantly opens and closes files.  
  
I will go for a call to `dup()` before using the `ip::tcp::socket` for now. But it is really hard for servers with a lot of clients.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:05:47 UTC  
> Url: https://github.com/boostorg/asio/issues/249#issuecomment-752292349  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#699](https://github.com/chriskohlhoff/asio/issues/699).
