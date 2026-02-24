# #1206 - sslv3 alert handshake failure - cloudflare [Closed]

> Username: ghost  
> Created at: 2018-07-23 12:19:10 UTC  
> Updated at: 2018-07-24 12:44:35 UTC  
> Closed at: 2018-07-23 15:17:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1206  

### Version of Beast  
  
BOOST_BEAST_VERSION 144  
  
### Steps necessary to reproduce the problem  
  
Use the websocket client SSL example to connect to a cloudflare-enabled websocket server.  
  
### Relevant Information  
  
After being able to successfully connect to multiple websocket servers without problems, I simply tried changing the server I connected to to one with cloudflare enabled. It keeps throwing a runtime error with the message:  
  
```  
libc++abi.dylib: terminating with uncaught exception of type boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::system::system_error> >: handshake: sslv3 alert handshake failure  
```  
  
I tried passing different TLS/SSL versions to the `boost::asio::ssl::context` but the error message would not change. Also tried calling `set_options` on the context to disable sslv3 from being used, but it didn't change the output as well.  
  
[https://github.com/chriskohlhoff/asio/issues/262](https://github.com/chriskohlhoff/asio/issues/262) may be related but its fixes were only applied to HTTP examples, not sure how to apply them to websockets since the websocket API is not the same.  
  
Any ideas on how to solve this problem?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-23 13:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1206#issuecomment-407052636  

> not sure how to apply them to websockets since the websocket API is not the same.  
  
Anything having to do with SSL would be applied to the `boost::asio::ssl::stream`, before any websocket handshaking takes place. So the use of WebSocket should not affect your fix.  
  
> Any ideas on how to solve this problem?  
  
None at all; this is strictly related to `ssl::stream` (OpenSSL in particular) and not Beast, and I am not an expert on TLS.

---

## Comment 2

> Username: ghost  
> Created at: 2018-07-23 15:07:14 UTC  
> Updated at: 2018-07-23 15:13:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1206#issuecomment-407091183  

Yea you're right, it's not a Beast problem. Do you know how I can access the underlying `ssl::stream` in this line of the example?   
  
```  
websocket::stream<ssl::stream<tcp::socket>> ws_;  
```  
  
Edit: I believe `ws_.next_layer()` should do the trick. Yep, I solved the problem using the solution outlined in [https://github.com/chriskohlhoff/asio/issues/262](https://github.com/chriskohlhoff/asio/issues/262)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-23 16:10:21 UTC  
> Updated at: 2018-07-23 16:10:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1206#issuecomment-407113113  

When you upgrade to the latest Beast you might want to use these declarations:  
```  
#include <boost/beast/experimental/core/ssl_stream.hpp>  
...  
boost::beast::websocket::stream<boost::beast::ssl_stream<tcp::socket>> ws;  
```  
See:  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__ssl_stream.html

---

## Comment 4

> Username: ghost  
> Created at: 2018-07-24 12:19:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1206#issuecomment-407386517  

Ah it seems to have a `native_handle`. Pretty neat, I will consider it thanks

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-07-24 12:44:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1206#issuecomment-407393531  

Well it also performs much better since it provides a work-around for a limitation in `asio::ssl::stream`.
