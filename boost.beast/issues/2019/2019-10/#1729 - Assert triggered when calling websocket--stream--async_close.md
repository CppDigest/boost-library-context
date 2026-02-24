# #1729 - Assert triggered when calling websocket::stream::async_close [Closed]

> Username: reddwarf69  
> Created at: 2019-10-09 16:49:17 UTC  
> Updated at: 2019-11-28 13:21:17 UTC  
> Closed at: 2019-11-28 13:21:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1729  

Running this code  
  
```  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <iostream>  
  
namespace asio = boost::asio;  
namespace ip = asio::ip;  
using tcp = ip::tcp;  
  
namespace beast = boost::beast;  
namespace websocket = beast::websocket;  
  
static auto tcp_socketpair(asio::io_context &ioc)  
    -> std::tuple<tcp::socket, tcp::socket> {  
  auto acceptor = tcp::acceptor{ioc, tcp::endpoint{tcp::v4(), 5000}};  
  
  auto client_socket = tcp::socket{ioc};  
  auto server_socket = tcp::socket{ioc};  
  
  acceptor.async_accept(server_socket, [](const boost::system::error_code &) {  
    std::cout << "TCP connection accepted" << std::endl;  
  });  
  client_socket.async_connect(  
      tcp::endpoint{ip::address::from_string("127.0.0.1"), 5000},  
      [](const boost::system::error_code &) {  
        std::cout << "TCP connection connected" << std::endl;  
      });  
  
  ioc.poll();  
  ioc.reset();  
  
  return std::make_tuple(std::move(client_socket), std::move(server_socket));  
}  
  
int main() {  
  auto ioc = asio::io_context{};  
  
  auto [client_socket, server_socket] = tcp_socketpair(ioc);  
  
  auto ws_client = websocket::stream<tcp::socket>{std::move(client_socket)};  
  auto ws_server = websocket::stream<tcp::socket>{std::move(server_socket)};  
  
  auto ws_timeout = websocket::stream_base::timeout{  
      websocket::stream_base::none(), // handshake timeout  
      std::chrono::seconds(30),       // idle timeout  
      true};  
  ws_server.set_option(ws_timeout);  
  
  ws_server.async_accept([](const beast::error_code &) {  
    std::cout << "WS connection accepted" << std::endl;  
  });  
  ws_client.async_handshake("lala.com", "/", [](const beast::error_code &) {  
    std::cout << "WS connection connected" << std::endl;  
  });  
  
  ioc.poll();  
  ioc.reset();  
  
  beast::flat_buffer buffer;  
  ws_server.async_read(buffer, asio::detached);  
  ws_server.async_close(websocket::close_reason{}, asio::detached);  
}  
```  
with either Boost 1.71 or develop HEAD results in:  
  
```  
TCP connection accepted  
TCP connection connected  
WS connection accepted  
WS connection connected  
la: ../install/include/boost/beast/websocket/impl/stream_impl.hpp:454: void boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::impl_type::update_timer(const Executor&) [with Executor = boost::asio::executor; NextLayer = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; bool deflateSupported = true]: Assertion `! is_timer_set()' failed.  
Aborted (core dumped)  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-09 17:02:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-540093521  

Investigating...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-10-09 19:01:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-540142608  

Please try this branch https://github.com/vinniefalco/beast/commits/develop

---

## Comment 3

> Username: reddwarf69  
> Created at: 2019-10-10 07:42:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-540440170  

I was trying to break it and found out this  
```  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <iostream>  
  
namespace asio = boost::asio;  
namespace ip = asio::ip;  
using tcp = ip::tcp;  
  
namespace beast = boost::beast;  
namespace websocket = beast::websocket;  
  
static auto tcp_socketpair(asio::io_context &ioc)  
    -> std::tuple<tcp::socket, tcp::socket> {  
  auto acceptor = tcp::acceptor{ioc, tcp::endpoint{tcp::v4(), 5000}};  
  
  auto client_socket = tcp::socket{ioc};  
  auto server_socket = tcp::socket{ioc};  
  
  acceptor.async_accept(server_socket, [](const boost::system::error_code &) {  
    std::cout << "TCP connection accepted" << std::endl;  
  });  
  client_socket.async_connect(  
      tcp::endpoint{ip::address::from_string("127.0.0.1"), 5000},  
      [](const boost::system::error_code &) {  
        std::cout << "TCP connection connected" << std::endl;  
      });  
  
  ioc.poll();  
  ioc.reset();  
  
  return std::make_tuple(std::move(client_socket), std::move(server_socket));  
}  
  
int main() {  
  auto ioc = asio::io_context{};  
  
  auto [client_socket, server_socket] = tcp_socketpair(ioc);  
  
  auto ws_client = websocket::stream<tcp::socket>{std::move(client_socket)};  
  auto ws_server = websocket::stream<tcp::socket>{std::move(server_socket)};  
  
  auto ws_timeout = websocket::stream_base::timeout{  
      websocket::stream_base::none(), // handshake timeout  
      std::chrono::seconds(1),        // idle timeout  
      false};  
  // ws_server.set_option(ws_timeout);  
  
  ws_server.async_accept([](const beast::error_code &) {  
    std::cout << "WS connection accepted" << std::endl;  
  });  
  ws_client.async_handshake("lala.com", "/", [](const beast::error_code &) {  
    std::cout << "WS connection connected" << std::endl;  
  });  
  
  ioc.poll();  
  ioc.reset();  
  
  // beast::flat_buffer buffer;  
  // ws_server.async_read(buffer, [&ws_server](const beast::error_code &ec,  
  // std::size_t) {std::cout << "WS read: " << ec.message() << std::endl/*;  
  // beast::get_lowest_layer(ws_server).cancel()*/;});  
  ws_server.async_close(  
      websocket::close_reason{}, [](const beast::error_code &ec) {  
        std::cout << "WS close: " << ec.message() << std::endl;  
      });  
  
  // sleep(2);  
  
  std::cout << ioc.stopped() << std::endl;  
  std::cout << ioc.poll() << std::endl;  
  std::cout << ioc.stopped() << std::endl;  
  std::cout << ioc.poll() << std::endl;  
}  
```  
prints  
```  
TCP connection accepted  
TCP connection connected  
WS connection accepted  
WS connection connected  
0  
2  
0  
0  
```  
  
Shouldn't it have printed `WS close`?

---

## Comment 4

> Username: reddwarf69  
> Created at: 2019-10-10 08:37:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-540463275  

Nevermind, I see it's https://github.com/boostorg/beast/issues/1618

---

## Comment 5

> Username: reddwarf69  
> Created at: 2019-10-10 09:22:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-540481056  

So now am using https://github.com/vinniefalco/beast/commits/develop and have  
  
```  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <iostream>  
  
namespace asio = boost::asio;  
namespace ip = asio::ip;  
using tcp = ip::tcp;  
  
namespace beast = boost::beast;  
namespace websocket = beast::websocket;  
  
static auto tcp_socketpair(asio::io_context &ioc)  
    -> std::tuple<tcp::socket, tcp::socket> {  
  auto acceptor = tcp::acceptor{ioc, tcp::endpoint{tcp::v4(), 5000}};  
  
  auto client_socket = tcp::socket{ioc};  
  auto server_socket = tcp::socket{ioc};  
  
  acceptor.async_accept(server_socket, [](const boost::system::error_code &) {  
    std::cout << "TCP connection accepted" << std::endl;  
  });  
  client_socket.async_connect(  
      tcp::endpoint{ip::address::from_string("127.0.0.1"), 5000},  
      [](const boost::system::error_code &) {  
        std::cout << "TCP connection connected" << std::endl;  
      });  
  
  ioc.poll();  
  ioc.reset();  
  
  return std::make_tuple(std::move(client_socket), std::move(server_socket));  
}  
  
int main() {  
  auto ioc = asio::io_context{};  
  
  auto [client_socket, server_socket] = tcp_socketpair(ioc);  
  
  auto ws_client = websocket::stream<tcp::socket>{std::move(client_socket)};  
  auto ws_server = websocket::stream<tcp::socket>{std::move(server_socket)};  
  
  auto ws_timeout = websocket::stream_base::timeout{  
      websocket::stream_base::none(), // handshake timeout  
      std::chrono::seconds(1),        // idle timeout  
      false};  
  ws_server.set_option(ws_timeout);  
  
  ws_server.async_accept([](const beast::error_code &) {  
    std::cout << "WS connection accepted" << std::endl;  
  });  
  ws_client.async_handshake("lala.com", "/", [](const beast::error_code &) {  
    std::cout << "WS connection connected" << std::endl;  
  });  
  
  ioc.poll();  
  ioc.reset();  
#ifdef FIRST_CLOSE  
  ws_server.async_close(  
      websocket::close_reason{}, [](const beast::error_code &ec) {  
        std::cout << "WS close: " << ec.message() << std::endl;  
      });  
#endif  
#ifdef DO_READ  
  beast::flat_buffer buffer;  
  ws_server.async_read(buffer,  
                       [&ws_server](const beast::error_code &ec, std::size_t) {  
                         std::cout << "WS read: " << ec.message() << std::endl;  
                       });  
#endif  
#ifdef SECOND_CLOSE  
  ws_server.async_close(  
      websocket::close_reason{}, [](const beast::error_code &ec) {  
        std::cout << "WS close: " << ec.message() << std::endl;  
      });  
#endif  
  
  std::cout << ioc.poll() << std::endl;  
  ioc.reset();  
  sleep(2);  
  std::cout << ioc.poll() << std::endl;  
  ioc.reset();  
  std::cout << ioc.poll() << std::endl;  
  ioc.reset();  
  
  sleep(10);  
  
  std::cout << "Done " << std::endl;  
}  
```  
  
* If built with DO_READ and FIRST_CLOSE I get  
```  
TCP connection accepted  
TCP connection connected  
WS connection accepted  
WS connection connected  
2  
0  
0  
Done  
```  
  
* If built with DO_READ and SECOND_CLOSE I get  
```  
TCP connection accepted  
TCP connection connected  
WS connection accepted  
WS connection connected  
2  
WS read: The socket was closed due to a timeout  
WS close: Bad file descriptor  
5  
0  
Done  
```  
  
So it looks like if I do the async_read after the async_close the idle timeout is never activated, and not being a handshake timeout it just never timeouts. But if I do the async_read before the async_close the idle_timeout is already activated and affects the async_close. Should the async_close call disable the idle timer if set?  
  
I'm actually a bit confused about https://github.com/boostorg/beast/issues/1618 anyway.  
Why was the change done?  
I'm now supposed to cancel any pending async_read before calling async_close?  
Let's say I call async_close() from my async_read() completion handler... should it also call async_read() again or not?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-10-22 19:46:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-545123138  

> So it looks like if I do the async_read after the async_close  
  
After `async_close` you aren't really supposed to issue any other async calls. Typically a program always has an outstanding call to `async_read`, so when `async_close` is called this `async_read` will already be outstanding.  
  
> The current implementation also reads and waits to receive a close frame, then performs a teardown.  
  
This is to handle the case where the user calls `async_close` when an async read completes.

---

## Comment 7

> Username: reddwarf69  
> Created at: 2019-10-23 11:15:54 UTC  
> Updated at: 2019-10-23 11:26:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-545394361  

> > So it looks like if I do the async_read after the async_close  
>   
> After `async_close` you aren't really supposed to issue any other async calls. Typically a program always has an outstanding call to `async_read`, so when `async_close` is called this `async_read` will already be outstanding.  
  
So you are saying the correct code is "DO_READ and SECOND_CLOSE", right?  
Notice in that case:  
- The idle timeout affects the closing handshake. Which is not totally unexpected, but also not obvious.  
- While async_read returns error::timeout, as expected, async_close returns "Bad file descriptor", which is totally unexpected. When people see that in the logs they are going to start chasing a non-existent bug in their apps.  
  
  
> > The current implementation also reads and waits to receive a close frame, then performs a teardown.  
>   
> This is to handle the case where the user calls `async_close` when an async read completes.  
  
To be 100% clear, are you saying async_close only "also reads and waits to receive a close frame" if there isn't a pre-existing pending async_read? Or does async_close always "also reads and waits to receive a close frame"?  
  
If it's the later FWIW personally I found the previous async_close behaviour simpler to handle. I didn't mind having to call `async_close(); async_read()` from my async_read completion handler. With the new async_close behaviour there are two operations doing the read, two operations which may timeout waiting to read the close frame, and the error reporting seems to get confusing. Even if the "Bad file descriptor" error from async_close were also a error::timeout... I would get two completion handlers reporting a timeout that has only happened once.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-10-23 12:39:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-545423602  

> To be 100% clear, are you saying async_close only "also reads and waits to receive a close frame" if there isn't a pre-existing pending async_read?  
  
Yes this  
  
> Or does async_close always "also reads and waits to receive a close frame"?  
  
This isn't possible, because there can only be one async_read outstanding on the underlying socket. The "soft mutex" is a form of synchronization that coordinates this:  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/close.hpp#L121  
  
In the line linked above, `try_lock` returns `false` because there is already an `async_read` that owns the soft mutex. So the async close operation is suspended (line 124).  
  
> async_close returns "Bad file descriptor", which is totally unexpected.  
  
I  agree this is undesirable, and I remember writing a test for this but now I can't seem to find it. The correct behavior is that `async_read` should complete with `error::closed`, and the `async_close` should complete with `error::operation_aborted`.

---

## Comment 9

> Username: reddwarf69  
> Created at: 2019-10-29 12:18:23 UTC  
> Updated at: 2019-10-29 12:19:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-547392533  

> > async_close returns "Bad file descriptor", which is totally unexpected.  
>   
> I agree this is undesirable, and I remember writing a test for this but now I can't seem to find it. The correct behavior is that `async_read` should complete with `error::closed`, and the `async_close` should complete with `error::operation_aborted`.  
  
So I guess you would expect https://github.com/boostorg/beast/blob/2ccb110dc97bc42890c685593481e3884303210a/include/boost/beast/websocket/impl/close.hpp#L129 to return true? (it doesn't)  
  
And it would do so because status is either status::closed or status::failed (https://github.com/boostorg/beast/blob/2ccb110dc97bc42890c685593481e3884303210a/include/boost/beast/websocket/impl/stream_impl.hpp#L344)? (it's `closing`).  
  
If so I don't really see where it was supposed to be set to status::closed. Maybe in the same place where timed_out is set to false to avoid a double timed_out report? (https://github.com/boostorg/beast/blob/2ccb110dc97bc42890c685593481e3884303210a/include/boost/beast/websocket/impl/stream_impl.hpp#L334) Or when the underlying socket is closed by the timer? (https://github.com/boostorg/beast/blob/2ccb110dc97bc42890c685593481e3884303210a/include/boost/beast/websocket/impl/stream_impl.hpp#L547)

---

## Comment 10

> Username: stale[bot]  
> Created at: 2019-11-28 13:08:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-559488279  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 11

> Username: reddwarf69  
> Created at: 2019-11-28 13:21:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1729#issuecomment-559492508  

Yes, it has.
