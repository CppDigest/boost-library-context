# #2065 - Assert on zero-sized read operation from beast::tcp_stream while using beast::ssl_stream [Closed]

> Username: villytiger  
> Created at: 2020-08-25 17:35:04 UTC  
> Updated at: 2020-09-14 06:58:31 UTC  
> Closed at: 2020-09-14 06:58:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2065  

Boost asio may run read operation with zero-sized buffer in some cases [1] even when it is write operation on a higher level. It works for asio::ip::tcp::socket since zero-sized read operation is in fact a no-op [2]. But I get assertion failure using beast::tcp_stream in the same case:  
```  
boost/beast/core/detail/stream_base.hpp:80: boost::beast::detail::stream_base::pending_guard::pending_guard(bool&): Assertion `! b_' failed.  
```  
  
### Version of Beast  
  
Beast version: 300  
Boost version: 1.74.0  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <boost/beast.hpp>  
#include <boost/beast/ssl.hpp>  
  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
  
int main() {  
        asio::io_context ioContext;  
        asio::ssl::context sslContext(asio::ssl::context::tlsv12_client);  
#ifdef FAIL  
        beast::ssl_stream<beast::tcp_stream> sslStream(ioContext, sslContext);  
#else  
        beast::ssl_stream<asio::ip::tcp::socket> sslStream(ioContext, sslContext);  
#endif  
  
        sslStream.next_layer().connect({ asio::ip::make_address_v4("173.194.222.113"), 443});  
        sslStream.handshake(asio::ssl::stream_base::client);  
  
        std::array<char, 1024> buffer;  
        sslStream.async_read_some(asio::buffer(buffer), [](auto ec, auto size) {});  
  
        sslStream.async_write_some(asio::buffer("", 0), [](auto ec, auto size) {});  
  
        ioContext.run();  
}  
```  
  
[1]: https://github.com/boostorg/asio/blob/boost-1.74.0/include/boost/asio/ssl/detail/io.hpp#L233  
[2]: https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/AsyncReadStream.html

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-25 17:40:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2065#issuecomment-680172405  

Oh! Great catch.  
I’ll check it out.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-09-02 14:40:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2065#issuecomment-685781575  

Hmm. I can't reproduce it. Is the above example the exact code to produce a problem?

---

## Comment 3

> Username: villytiger  
> Created at: 2020-09-02 15:40:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2065#issuecomment-685820893  

Did you compile it with `-DFAIL`? I made two branches to demonstrate that it works for Asio stream. I should have made the Beast branch the default.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-09-02 17:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2065#issuecomment-685874241  

OK, I have it reproduced. Working on it.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-09-03 09:09:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2065#issuecomment-686359641  

Deduced that this is actually a bug in Asio's `ssl::stream` - it breaks the `AsyncReadStream` preconditions of the underlying stream when performing a 0-length write by issuing a 0-length async read against the underlying stream in lieu of a `post()`. The post/async_op is necessary to ensure that the supplied completion handler is invoked by the correct executor. The use of a 0-length read is there as a means of reducing template expansions - it is a compile-time optimisation.  
  
Strictly, Asio is in the wrong and Beast is in the right on this one, but for the sake of our users I think we need to handle this corner case gracefully.
