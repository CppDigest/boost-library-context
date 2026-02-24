# #137 - compile error when returning a websocket stream from a coroutine [Closed]

> Username: matthijs  
> Created at: 2023-11-05 20:17:59 UTC  
> Updated at: 2023-11-07 01:14:14 UTC  
> Closed at: 2023-11-07 01:14:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/137  

Hi,  
  
Here is a small reproducible example of what I am trying to achieve:  
```c++  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/cobalt.hpp>  
#include <boost/url/url.hpp>  
#include <boost/url/parse.hpp>  
  
namespace cobalt = boost::cobalt;  
  
// Needed types  
using executor_type = cobalt::use_op_t::executor_with_default<cobalt::executor>;  
using socket_type = typename boost::asio::ip::tcp::socket::rebind_executor<executor_type>::other;  
using ssl_socket_type = boost::asio::ssl::stream<socket_type>;  
using websocket_type = boost::beast::websocket::stream<ssl_socket_type>;  
  
cobalt::promise<ssl_socket_type> connect(std::string_view host, std::string_view port, boost::asio::ssl::context& ctx)  
{  
  // Resolve possible endpoints  
  boost::asio::ip::tcp::resolver resolve{cobalt::this_thread::get_executor()};  
  auto endpoints = co_await resolve.async_resolve(host, port, cobalt::use_op);  
  
  // Connect to one of the endpoints  
  ssl_socket_type sock{cobalt::this_thread::get_executor(), ctx};  
  co_await boost::asio::async_connect(sock.lowest_layer(), endpoints);  
  
  // Handshake  
  co_await sock.async_handshake(boost::asio::ssl::stream_base::client);  
  
  // Return the connected socket  
  co_return sock;  
}  
  
// connect/disconnect websocket  
cobalt::promise<websocket_type> connect_ws(boost::urls::url_view uri, boost::asio::ssl::context& ctx)  
{  
  // Create a websocket stream and connect to the endpoint  
  websocket_type ws {co_await connect(uri.host(), uri.port(), ctx)};  
  
  // Websocket handshake  
  co_await ws.async_handshake(uri.host(), uri.encoded_target());  
  
  // Return the websocket  
  co_return ws;  
}  
  
cobalt::main co_main(int argc, char** argv)  
{  
  boost::asio::ssl::context ctx{boost::asio::ssl::context::tlsv12_client};  
  boost::urls::url uri = boost::urls::parse_uri("wss://socketsbay.com/wss/v2/1/demo/").value();  
  auto ws = co_await connect_ws(uri, ctx);  
  
  co_return 0;  
}  
```  
  
When I try to compile this:  
```c++  
$ clang++-16 -o test -std=c++20 -isystem /home/matthijs/src/boost/boost_1_83_0 test.cpp  
In file included from test.cpp:7:  
In file included from /usr/local/include/boost/cobalt.hpp:23:  
In file included from /usr/local/include/boost/cobalt/promise.hpp:11:  
/usr/local/include/boost/cobalt/detail/promise.hpp:59:12: error: object of type 'std::optional<stream<stream<basic_stream_socket<tcp, executor_with_default<any_io_executor>>>, true>>' cannot be assigned because its copy assignment operator is implicitly deleted  
    result = std::move(ret);  
           ^  
/usr/local/include/boost/cobalt/detail/promise.hpp:260:17: note: in instantiation of member function 'boost::cobalt::detail::promise_value_holder<boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::cobalt::use_op_t::executor_with_default<boost::asio::any_io_executor>>>>>::return_value' requested here  
      receiver->return_value(std::move(ret));  
                ^  
test.cpp:46:3: note: in instantiation of member function 'boost::cobalt::detail::cobalt_promise_result<boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::cobalt::use_op_t::executor_with_default<boost::asio::any_io_executor>>>>>::return_value' requested here  
  co_return ws;  
  ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/10/../../../../include/c++/10/optional:661:7: note: copy assignment operator of 'optional<boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::cobalt::use_op_t::executor_with_default<boost::asio::any_io_executor>>>>>' is implicitly deleted because base class '_Enable_copy_move<is_copy_constructible_v<stream<stream<basic_stream_socket<tcp, executor_with_default<any_io_executor>>>, true>>, __and_v<is_copy_constructible<stream<stream<basic_stream_socket<tcp, executor_with_default<any_io_executor>>>, true>>, is_copy_assignable<stream<stream<basic_stream_socket<tcp, executor_with_default<any_io_executor>>>, true>>>, is_move_constructible_v<stream<stream<basic_stream_socket<tcp, executor_with_default<any_io_executor>>>, true>>, __and_v<is_move_constructible<stream<stream<basic_stream_socket<tcp, executor_with_default<any_io_executor>>>, true>>, is_move_assignable<stream<stream<basic_stream_socket<tcp, executor_with_default<any_io_executor>>>, true>>>, optional<stream<stream<basic_stream_socket<tcp, executor_with_default<any_io_executor>>>, true>>>' has a deleted copy assignment operator  
      private _Enable_copy_move<  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/10/../../../../include/c++/10/bits/enable_special_members.h:256:5: note: 'operator=' has been explicitly marked deleted here  
    operator=(_Enable_copy_move const&) noexcept                    = delete;  
    ^  
1 error generated.  
```  
What am I missing here?

---

## Comment 1

> Username: matthijs  
> Created at: 2023-11-05 20:56:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/137#issuecomment-1793843503  

In the end I am trying to achieve something like this:  
```c++  
cobalt::promise<void> session(websocket_type& ws)  
{  
  // Do something useful with ws  
  co_return;  
}  
  
auto teardown(websocket_type ws, std::exception_ptr ep)  
{  
  return disconnect_ws(ws);  
}  
  
cobalt::main co_main(int argc, char** argv)  
{  
  boost::asio::ssl::context ctx{boost::asio::ssl::context::tlsv12_client};  
  boost::urls::url uri = boost::urls::parse_uri("wss://socketsbay.com/wss/v2/1/demo/").value();  
  co_await cobalt::with(co_await connect_ws(uri, ctx), &session, &teardown);  
  
  co_return 0;  
}  
```
