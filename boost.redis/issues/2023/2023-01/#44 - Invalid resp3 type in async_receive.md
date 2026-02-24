# #44 - Invalid resp3 type in async_receive [Closed]

> Username: nejati-deriv  
> Created at: 2023-01-18 19:09:56 UTC  
> Updated at: 2023-01-30 07:03:32 UTC  
> Closed at: 2023-01-30 07:03:32 UTC  
> Url: https://github.com/boostorg/redis/issues/44  

You can use the following links to reproduce:  
Receiver side: https://godbolt.org/z/TzM9T8P3x  
Publisher side: https://godbolt.org/z/WaavdKGnc  
  
Observations:  
- If you run sender and receiver form one connection it doesn't happens.  
- It seems the content of the published message is important (happens for some contents).

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-01-18 20:19:31 UTC  
> Url: https://github.com/boostorg/redis/issues/44#issuecomment-1387726064  

The content you are sending on the publisher is humm is this also causing the problem?

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-01-18 20:49:05 UTC  
> Updated at: 2023-01-18 20:59:32 UTC  
> Url: https://github.com/boostorg/redis/issues/44#issuecomment-1396067837  

I can reproduce, but only in release mode, not in debug.

---

## Comment 3

> Username: nejati-deriv  
> Created at: 2023-01-19 07:06:38 UTC  
> Url: https://github.com/boostorg/redis/issues/44#issuecomment-1396530249  

> I can reproduce, but only in release mode, not in debug.  
  
It might be dependent to memory layout, I'm using clang-15 and it occurs in debug too.  
Try another message that might happens in debug too.

---

## Comment 4

> Username: mzimbres  
> Created at: 2023-01-19 07:12:20 UTC  
> Url: https://github.com/boostorg/redis/issues/44#issuecomment-1396535312  

A subscriber implemented with low-level functions doesn't suffer from that problem, so it looks like it is not a parser problem. I it receives EOF from the server, perhaps for being able to keep-up with the high volume of messages, I have to measure more, see below  
  
```cpp  
#include <boost/asio.hpp>  
#include <iostream>  
  
#include <aedis.hpp>  
#include <aedis/src.hpp>  
  
namespace net = boost::asio;  
namespace resp3 = aedis::resp3;  
using resolver = net::use_awaitable_t<>::as_default_on_t<net::ip::tcp::resolver>;  
using tcp_socket = net::use_awaitable_t<>::as_default_on_t<net::ip::tcp::socket>;  
using aedis::adapter::adapt2;  
using net::ip::tcp;  
  
auto co_main(std::string host, std::string port) -> net::awaitable<void>  
{  
   try {  
   auto ex = co_await net::this_coro::executor;  
  
   resolver resv{ex};  
   auto const addrs = co_await resv.async_resolve("127.0.0.1", "6379");  
   tcp_socket socket{ex};  
   co_await net::async_connect(socket, addrs);  
  
   // Creates the request and writes to the socket.  
   resp3::request req;  
   req.push("HELLO", 3);  
   req.push("SUBSCRIBE", "test-channel");  
   co_await resp3::async_write(socket, req);  
  
   std::string buffer, resp;  
  
   auto dbuffer = net::dynamic_buffer(buffer);  
   for (;;) {  
      co_await resp3::async_read(socket, dbuffer);  
      //std::cout << "ddd" << std::endl;  
   }  
   } catch(std::exception const& e) {  
      std::cout << e.what() << std::endl;  
   }  
}  
  
int main()  
{  
    net::io_context ioc{};  
    net::co_spawn(ioc, co_main("127.0.0.1", "6379"), net::detached);  
    ioc.run();  
}  
  
```

---

## Comment 5

> Username: nejati-deriv  
> Created at: 2023-01-19 12:37:36 UTC  
> Url: https://github.com/boostorg/redis/issues/44#issuecomment-1396913077  

It seems max_read_size in make_dynamic_buffer is related to this issue, tuning it to something higher for example 512000 solves the issue.  
https://github.com/mzimbres/aedis/blob/c88fcfb9edbaca3cc90e761d8b3087c0eed4a9fb/include/aedis/detail/connection_base.hpp#L306

---

## Comment 6

> Username: mzimbres  
> Created at: 2023-01-28 08:49:24 UTC  
> Url: https://github.com/boostorg/redis/issues/44#issuecomment-1407343209  

Fixed in commit https://github.com/mzimbres/aedis/commit/56c0b280033886510e3f91f4704f4ed87da4a14d
