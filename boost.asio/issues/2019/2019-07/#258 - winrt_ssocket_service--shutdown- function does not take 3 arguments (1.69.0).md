# #258 - winrt_ssocket_service::shutdown: function does not take 3 arguments (1.69.0) [Closed]

> Username: avakhrushev  
> Created at: 2019-07-08 10:57:04 UTC  
> Updated at: 2020-12-30 01:06:40 UTC  
> Closed at: 2020-12-30 01:06:39 UTC  
> Url: https://github.com/boostorg/asio/issues/258  

Using boost::asio::ip::tcp::socket::shutdown(tcp::socket::shutdown_both, ec);  
(Edit: referenced indirectly from basic_socket::shutdown)  
I get this error during build:  
'boost::asio::detail::winrt_ssocket_service<Protocol>::shutdown': function does not take 3 arguments  
  
It appears winrt_ssocket_service does not in fact declare shutdown() with three parameters; it is declared at https://github.com/boostorg/asio/blob/boost-1.69.0/include/boost/asio/detail/winrt_ssocket_service_base.hpp#L168  
  
Is this a bug, and I should just declare missing shutdown() in winrt_ssocket_service,   
or am I missing some steps necessary for WinRT in configuration maybe?

---

## Comment 1

> Username: djarek  
> Created at: 2019-07-08 19:16:27 UTC  
> Url: https://github.com/boostorg/asio/issues/258#issuecomment-509355828  

The function definition you linked does have 3 arguments.

---

## Comment 2

> Username: avakhrushev  
> Created at: 2019-07-09 06:40:31 UTC  
> Url: https://github.com/boostorg/asio/issues/258#issuecomment-509510751  

Yes it does. It is still not declared and is not available in the class that is actually used. Winrt_ssocket_service and winrt_ssocket_service_base are different classes.

---

## Comment 3

> Username: djarek  
> Created at: 2019-07-09 21:33:28 UTC  
> Url: https://github.com/boostorg/asio/issues/258#issuecomment-509818074  

Oh, right, I missed the `_base` suffix in the name.

---

## Comment 4

> Username: avakhrushev  
> Created at: 2019-09-02 15:20:01 UTC  
> Url: https://github.com/boostorg/asio/issues/258#issuecomment-527182974  

Appears to have been addressed https://github.com/boostorg/asio/commit/78f22b5a6e77aeb8c63c876d6b41d22d936d3b2b#diff-bb37c96539aea0e4b0de780b82b94890

---

## Comment 5

> Username: ghost  
> Created at: 2020-12-30 01:06:38 UTC  
> Url: https://github.com/boostorg/asio/issues/258#issuecomment-752292497  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#703](https://github.com/chriskohlhoff/asio/issues/703).
