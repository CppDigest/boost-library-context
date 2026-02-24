# #113 - new example: Http server [Closed]

> Username: kassane  
> Created at: 2023-09-08 14:56:33 UTC  
> Updated at: 2023-09-08 18:20:26 UTC  
> Closed at: 2023-09-08 17:47:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/113  

Hi @klemens-morgenstern,  
  
I've been following this project since cppslack. Nice hard work to make this library.  
So today, using my free time, I tried out a brief test based on the examples to make an http server.  
The connection with the curl and web browser clients started working locally.  
  
<details>  
<summary>Code - HTTP Server sample</summary>  
  
```c++  
#include <boost/async.hpp>  
#include <boost/async/main.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast.hpp>  
#include <iostream>  
namespace async = boost::async;  
using boost::asio::ip::tcp;  
using tcp_acceptor = async::use_op_t::as_default_on_t<tcp::acceptor>;  
using tcp_socket = async::use_op_t::as_default_on_t<tcp::socket>;  
namespace this_coro = boost::async::this_coro;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
async::promise<void> handle_request(tcp_socket socket) {  
    try {  
        beast::flat_buffer buffer;  
        http::request<http::string_body> request;  
  
        co_await http::async_read(socket, buffer, request);  
  
        http::response<http::string_body> response(http::status::ok, request.version());  
        response.set(http::field::server, "Boost/AsyncHTTPServer");  
  
        // Extract the requested path from the HTTP request.  
        std::string_view path = request.target();  
  
        // Define different routes and their corresponding responses.  
        if (path == "/") {  
            response.body() = "Welcome to the root route!";  
        } else if (path == "/hello") {  
            response.body() = "Hello, World!";  
        } else {  
            response = http::response<http::string_body>(http::status::not_found, request.version());  
            response.body() = "404 Not Found";  
        }  
  
        response.prepare_payload();  
        co_await http::async_write(socket, response);  
    } catch (std::exception& e) {  
        std::cerr << "HTTP Request Handling Exception: " << e.what() << std::endl;  
    }  
}  
  
async::generator<tcp_socket> listen() {  
    tcp_acceptor acceptor({ co_await this_coro::executor }, { tcp::v4(), 8080 });  
  
    for (;;) {  
        co_yield co_await acceptor.async_accept();  
    }  
}  
  
async::promise<void> run_server(async::wait_group& workers) {  
    auto l = listen();  
  
    while (true) {  
        if (workers.size() < 10u)  
            workers.push_back(handle_request(co_await l));  
        else  
            co_await workers.wait_one();  
    }  
}  
  
async::main co_main(int argc, char** argv) {  
    co_await async::with(async::wait_group(), &run_server);  
    co_return 0u;  
}  
```  
</details>  
  
However, going a step beyond and using (`wrk -t1 -c4 -d10s http://127.0.0.1:8080/`) to stress test, received a long error.  
  
```bash  
HTTP Request Handling Exception: end of stream [beast.http:1 at /home/kassane/Documentos/async/zig-cache/i/799e64629e39350119128922a98f457f/include/boost/beast/http/impl/read.hpp:231:67 in function 'void boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, boost::beast::basic_flat_buffer<std::allocator<char>>, true>::operator()(Self &, error_code, std::size_t) [AsyncReadStream = boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, DynamicBuffer = boost::beast::basic_flat_buffer<std::allocator<char>>, isRequest = true, Self = boost::asio::detail::composed_op<boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, boost::beast::basic_flat_buffer<std::allocator<char>>, true>, boost::asio::detail::composed_work<void (boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>)>, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, boost::beast::basic_flat_buffer<std::allocator<char>>, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>)>, boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, boost::beast::basic_flat_buffer<std::allocator<char>>, true, boost::beast::http::basic_string_body<char>, std::allocator<char>, boost::async::completion_handler<boost::system::error_code, unsigned long>>, void (boost::system::error_code, unsigned long)>, void (boost::system::error_code, unsigned long)>]']  
HTTP Request Handling Exception: end of stream [beast.http:1 at /home/kassane/Documentos/async/zig-cache/i/799e64629e39350119128922a98f457f/include/boost/beast/http/impl/read.hpp:231:67 in function 'void boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, boost::beast::basic_flat_buffer<std::allocator<char>>, true>::operator()(Self &, error_code, std::size_t) [AsyncReadStream = boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, DynamicBuffer = boost::beast::basic_flat_buffer<std::allocator<char>>, isRequest = true, Self = boost::asio::detail::composed_op<boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, boost::beast::basic_flat_buffer<std::allocator<char>>, true>, boost::asio::detail::composed_work<void (boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>)>, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, boost::beast::basic_flat_buffer<std::allocator<char>>, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>)>, boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::async::use_op_t::executor_with_default<boost::asio::any_io_executor>>, boost::beast::basic_flat_buffer<std::allocator<char>>, true, boost::beast::http::basic_string_body<char>, std::allocator<char>, boost::async::completion_handler<boost::system::error_code, unsigned long>>, void (boost::system::error_code, unsigned long)>, void (boost::system::error_code, unsigned long)>]']  
```  
  
**Edit:** Same error, using `GCC` 13.2 w/ cmake (archlinux)  
```bash  
./build_rel/example/boost_async_example_http_server   
HTTP Request Handling Exception: end of stream [beast.http:1 at /usr/include/boost/beast/http/impl/read.hpp:231:21 in function 'void boost::beast::http::detail::read_some_op<AsyncReadStream, DynamicBuffer, isRequest>::operator()(Self&, boost::beast::error_code, std::size_t)']  
HTTP Request Handling Exception: end of stream [beast.http:1 at /usr/include/boost/beast/http/impl/read.hpp:231:21 in function 'void boost::beast::http::detail::read_some_op<AsyncReadStream, DynamicBuffer, isRequest>::operator()(Self&, boost::beast::error_code, std::size_t)']  
HTTP Request Handling Exception: end of stream [beast.http:1 at /usr/include/boost/beast/http/impl/read.hpp:231:21 in function 'void boost::beast::http::detail::read_some_op<AsyncReadStream, DynamicBuffer, isRequest>::operator()(Self&, boost::beast::error_code, std::size_t)']  
```  
  
Maybe, need threadpool?  
  
---  
  
**Note:**  
The error log will show `zig-cache` because I've used its built-in clang to build. Because, my [async-fork](https://github.com/klemens-morgenstern/async/compare/master...kassane:async:zig-pkg) uses the zig build-system.  
  
About the only real difference between conventional and `zig c++` is that the zig toolchain only uses statically compiled and linked `llvm-libcxx` + `llvm-libunwind`, in addition to custom `libcompiler-rt` (rewritten in ziglang).  
  
But feel free to test it on your preferred toolchain.

---

## Comment 1

> Username: kassane  
> Created at: 2023-09-08 17:47:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/113#issuecomment-1712026226  

This error no shorter persists after the following change.  
  
```diff  
- co_await http::async_read(socket, buffer, request);  
+  co_await http::async_read(socket, buffer, request, boost::asio::as_tuple(async::use_op));  
- co_await http::async_write(socket, response);  
+ co_await http::async_write(socket, response, boost::asio::as_tuple(async::use_op));
