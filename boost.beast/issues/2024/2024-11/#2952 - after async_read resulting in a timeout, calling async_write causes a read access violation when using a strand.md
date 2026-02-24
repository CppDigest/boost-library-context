# #2952 - after async_read resulting in a timeout, calling async_write causes a read access violation when using a strand [Closed]

> Username: mgroenhoff  
> Created at: 2024-11-14 14:15:43 UTC  
> Updated at: 2024-11-28 12:12:24 UTC  
> Closed at: 2024-11-28 12:12:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2952  

I was attempting to return nice error responses for certain errors. For example i'd like to return a 408 when a timeout occurs. However it seems to also close the socket.  
  
While testing this out I ran into a access violation in `boost::asio::detail::strand_executor_service::enqueue` while acquiring a mutex lock and it seems the strand is already destructed (`impl` is `nullptr`). When not using a strand the next `async_write` simply also returns the same error. I've tried different completion tokens but that doesn't change the result. It looks like the strand is cleaned up together with the socket so I guess I either need to:  
- somehow extend the lifetime of the strand beyond the socket  
- or manually start a timer instead of using `stream.expires_after` so it keeps the socket alive  
  
Is this a bug in either beast or asio or am I using the strands wrong. I used https://www.boost.org/doc/libs/1_86_0/libs/beast/example/advanced/server-flex-awaitable/advanced_server_flex_awaitable.cpp for inspiration.  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
  
#include <iostream>  
  
#define USE_STRAND 1  
#define USE_DEFERRED 1  
  
#ifdef USE_STRAND  
using executor_type = boost::asio::strand<boost::asio::io_context::executor_type>;  
#else  
using executor_type = boost::asio::io_context::executor_type;  
#endif  
  
using tcp_acceptor_type = boost::asio::ip::tcp::acceptor::rebind_executor<executor_type>::other;  
using tcp_socket_type = boost::asio::ip::tcp::socket::rebind_executor<executor_type>::other;  
using tcp_stream_type = boost::beast::tcp_stream::rebind_executor<executor_type>::other;  
  
#ifdef USE_DEFERRED  
constexpr auto completion_token = boost::asio::as_tuple(boost::asio::deferred);  
#else  
constexpr auto completion_token = boost::asio::as_tuple;  
#endif  
  
constexpr int num_threads = 1;  
  
boost::beast::http::message_generator simple_result_response(boost::beast::http::status result, unsigned version, bool keep_alive) {  
    boost::beast::http::response<boost::beast::http::string_body> response{result, version};  
    response.keep_alive(keep_alive);  
    response.set(boost::beast::http::field::content_type, "text/plain; charset=UTF-8");  
    response.body() = boost::beast::http::obsolete_reason(response.result());  
    response.prepare_payload();  
    return {std::move(response)};  
}  
  
boost::asio::awaitable<void, executor_type> session(tcp_socket_type socket) {  
    tcp_stream_type stream{std::move(socket)};  
    boost::beast::flat_buffer buffer;  
    bool keep_alive;  
  
    do {  
        stream.expires_after(std::chrono::seconds{5});  
  
        boost::beast::http::request_parser<boost::beast::http::string_body> request_parser;  
        {  
            auto [ec, _] = co_await boost::beast::http::async_read(stream, buffer, request_parser, completion_token);  
            if (ec.failed()) {  
                std::println(std::cerr, "error from async_read(): {}", ec.message());  
                // currently only timeout is handled but one can for example also return a bad_request for boost::beast::http::error::bad_* errors.  
                if (ec == boost::beast::error::timeout) {  
                    // if async_read errors because of a timeout, the next async_write does too. Except if using a strand in which case it results in a read access violation:  
                    //   std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl>::operator-><boost::asio::detail::strand_executor_service::strand_impl,0>(...) returned nullptr.  
                    auto [ec2, _] = co_await boost::beast::async_write(stream, simple_result_response(boost::beast::http::status::request_timeout, request_parser.get().version(), false), completion_token);  
                    if (ec2.failed()) {  
                        std::println(std::cerr, "error from async_write() while sending error response: {}", ec2.message());  
                    }  
                }  
                break;  
            }  
        }  
  
        auto response = simple_result_response(boost::beast::http::status::not_found, request_parser.get().version(), request_parser.keep_alive());  
        keep_alive = response.keep_alive();  
        {  
            auto [ec, _] = co_await boost::beast::async_write(stream, std::move(response), completion_token);  
            if (ec.failed()) {  
                std::println(std::cerr, "error from async_write(): {}", ec.message());  
                break;  
            }  
        }  
    } while (keep_alive);  
  
    if (stream.socket().is_open()) {  
        stream.socket().shutdown(boost::asio::ip::tcp::socket::shutdown_send);  
    }  
}  
  
boost::asio::awaitable<void, executor_type> listen(boost::asio::ip::tcp::endpoint endpoint) {  
    auto executor = co_await boost::asio::this_coro::executor;  
  
    tcp_acceptor_type tcp_acceptor{executor, boost::asio::ip::tcp::endpoint{boost::asio::ip::tcp::v4(), 80}};  
  
    for (;;) {  
#ifdef USE_STRAND  
        auto socket_executor = boost::asio::make_strand(executor.get_inner_executor());  
#else  
        auto socket_executor = executor;  
#endif  
        auto [ec, socket] = co_await tcp_acceptor.async_accept(socket_executor, completion_token);  
        if (ec.failed()) {  
            std::println(std::cerr, "error from async_accept(): {}", ec.message());  
            break;  
        }  
  
        boost::asio::co_spawn(  
            std::move(socket_executor),  
            session(std::move(socket)),  
            [](std::exception_ptr e) {  
                if (e) {  
                    try {  
                        std::rethrow_exception(e);  
                    } catch(const std::exception& e) {  
                        std::println(std::cerr, "error in session: {}", e.what());  
                    }  
                }  
            }  
        );  
    }  
}  
  
int main() {  
    boost::asio::io_context io_context{num_threads};  
  
    boost::asio::co_spawn(  
#ifdef USE_STRAND  
        boost::asio::make_strand(io_context),  
#else  
        io_context,  
#endif  
        listen({boost::asio::ip::tcp::v4(), 80}),  
        [](std::exception_ptr e) {  
            if (e) {  
                try {  
                    std::rethrow_exception(e);  
                } catch(const std::exception& e) {  
                    std::println(std::cerr, "error in listener: {}", e.what());  
                }  
            }  
        }  
    );  
  
    std::vector<std::jthread> threads;  
    if constexpr (num_threads > 1) {  
        threads.reserve(num_threads - 1);  
        for (int i = num_threads - 1; i > 0; --i) {  
            threads.emplace_back([&io_context]() {  
                io_context.run();  
            });  
        }  
    }  
  
    io_context.run();  
  
    return EXIT_SUCCESS;  
}  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-11-14 15:49:56 UTC  
> Updated at: 2024-11-14 15:55:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2952#issuecomment-2476763750  

You need to update the internal timeout after the fist expiration by calling `stream.expires_after` before the next operation. If you want independent control over read and write timeouts, consider using the [asio::cancel_after](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/cancel_after.html) interface.

---

## Comment 2

> Username: mgroenhoff  
> Created at: 2024-11-15 08:57:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2952#issuecomment-2478270922  

Thanks @ashtum that seems to work. I must have missed that when reading the docs. Can you point me to it?

---

## Comment 3

> Username: ashtum  
> Created at: 2024-11-15 14:58:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2952#issuecomment-2479081261  

> Thanks [@ashtum](https://github.com/ashtum) that seems to work. I must have missed that when reading the docs. Can you point me to it?  
  
[basic_stream::expires_after](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__basic_stream/expires_after.html)  
> The timer applies collectively to any asynchronous reads or writes initiated after the expiration is set, until the expiration is set again. A call to [async_connect](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__basic_stream/async_connect.html) counts as both a read and a write.

---

## Comment 4

> Username: mgroenhoff  
> Created at: 2024-11-15 16:33:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2952#issuecomment-2479385219  

> If a timer expires when the corresponding asynchronous operation is outstanding, the stream will be closed and any outstanding operations will complete with the error [beast::error::timeout](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__error.html).  
  
According to the above, when a timeout occurs while `async_read` is outstanding the stream will be closed. But that doesn't seem to be the case. As you point out I can call `expires_after` again (or `expires_never`) which will keep the stream alive. At which point does it actually close the stream? I can't get that from the docs.  
  
> The timer applies collectively to any asynchronous reads or writes initiated after the expiration is set, until the expiration is set again.  
  
What does that mean exactly? Does that mean that I should be able to call `async_read` (or any other read or write) and expect another beast::error::timeout?  
  
In the example above the behavior is different when using a strand and not using one. I did not expect a access violation when initiating a read or write after a timeout but instead just a timeout error as is the case if I don't use the strand.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-11-15 17:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2952#issuecomment-2479601017  

> According to the above, when a timeout occurs while `async_read` is outstanding the stream will be closed. But that doesn't seem to be the case. As you point out I can call `expires_after` again (or `expires_never`) which will keep the stream alive. At which point does it actually close the stream? I can't get that from the docs.  
  
Sorry, I mistakenly assumed you were checking `ec` for `error::operation_aborted` instead of `error::timeout`. When a [beast::tcp_stream](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__tcp_stream.html) timer expires, it closes the underlying stream, preventing further operations. If you want to perform another operation after a timeout, consider using the [asio::cancel_after](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/cancel_after.html) interface.  
  
> > The timer applies collectively to any asynchronous reads or writes initiated after the expiration is set, until the expiration is set again.  
>   
> What does that mean exactly? Does that mean that I should be able to call `async_read` (or any other read or write) and expect another beast::error::timeout?  
  
When you set an expiration time, it applies to all async operations initiated after that, not just one. To extend the timeout, you need to set a new expiration time before each operation.  
  
> In the example above the behavior is different when using a strand and not using one. I did not expect a access violation when initiating a read or write after a timeout but instead just a timeout error as is the case if I don't use the strand.  
  
It seems you're encountering this issue: https://github.com/boostorg/beast/issues/2925. It's already fixed and will be included in the next Boost release (Boost 1.87).
