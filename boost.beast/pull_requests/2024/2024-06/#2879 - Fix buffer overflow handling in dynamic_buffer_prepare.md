# #2879 Fix buffer overflow handling in dynamic_buffer_prepare [Closed]

> Username: tyler92  
> Created at: 2024-06-02 18:53:59 UTC  
> Updated at: 2024-06-06 20:49:27 UTC  
> Closed at: 2024-06-05 21:19:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2879  

I experienced a bad_alloc exception using WebSocket server with enabled compression. A malformed message with a very big length led to the following address sanitizer report:  
  
```  
==1739459==ERROR: AddressSanitizer: requested allocation size 0x4000000ffff073e (0x4000000ffff1740 after adjustments for alignment, red zones etc.) exceeds maximum supported size of 0x10000000000 (thread T0)  
    #0 0x7f494ecf91e7 in operator new(unsigned long) ../../../../src/libsanitizer/asan/asan_new_delete.cpp:99  
    #1 0x5561b0a62199 in __gnu_cxx::new_allocator<char>::allocate(unsigned long, void const*) /usr/include/c++/11/ext/new_allocator.h:127  
    #2 0x5561b0a54840 in std::allocator_traits<std::allocator<char> >::allocate(std::allocator<char>&, unsigned long) /usr/include/c++/11/bits/alloc_traits.h:464  
    #3 0x5561b0a8bd68 in boost::beast::basic_flat_buffer<std::allocator<char> >::alloc(unsigned long) boost/include/boost/beast/core/impl/flat_buffer.hpp:538  
    #4 0x5561b0a7a840 in boost::beast::basic_flat_buffer<std::allocator<char> >::prepare(unsigned long) boost/include/boost/beast/core/impl/flat_buffer.hpp:351  
    #5 0x5561b0a6b9e4 in boost::optional<boost::beast::basic_flat_buffer<std::allocator<char> >::mutable_buffers_type> boost::beast::detail::dynamic_buffer_prepare<boost::beast::basic_flat_buffer<std::allocator<char> >, boost::beast::websocket::error>(boost::beast::basic_flat_buffer<std::allocator<char> >&, unsigned long, boost::system::error_code&, boost::beast::websocket::error) boost/include/boost/beast/core/detail/buffer.hpp:64  
    #6 0x5561b0a5b230 in boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >::operator()(boost::system::error_code, unsigned long, bool) boost/include/boost/beast/websocket/impl/read.hpp:791  
    #7 0x5561b0b53d95 in void boost::asio::detail::prepend_handler<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::system::error_code, unsigned long>::invoke<0ul, 1ul>(std::integer_sequence<unsigned long, 0ul, 1ul>) boost/include/boost/asio/impl/prepend.hpp:56  
    #8 0x5561b0b42861 in void boost::asio::detail::prepend_handler<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::system::error_code, unsigned long>::operator()<>() boost/include/boost/asio/impl/prepend.hpp:48  
    #9 0x5561b0b34679 in boost::asio::detail::binder0<boost::asio::detail::prepend_handler<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::system::error_code, unsigned long> >::operator()() boost/include/boost/asio/detail/bind_handler.hpp:56  
    #10 0x5561b0b34b7a in void boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<boost::asio::detail::prepend_handler<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::system::error_code, unsigned long> >, std::allocator<void> >(boost::asio::detail::executor_function::impl_base*, bool) boost/include/boost/asio/detail/executor_function.hpp:113  
    #11 0x5561b09bfefe in boost::asio::detail::executor_function::operator()() boost/include/boost/asio/detail/executor_function.hpp:61  
    #12 0x5561b0b9740b in void boost::asio::detail::strand_executor_service::do_execute<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const, boost::asio::detail::executor_function, std::allocator<void> >(std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const&, boost::asio::detail::executor_function&&, std::allocator<void> const&) boost/include/boost/asio/detail/impl/strand_executor_service.hpp:233  
    #13 0x5561b0b822d3 in void boost::asio::detail::strand_executor_service::execute<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const, boost::asio::detail::executor_function>(std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const&, boost::asio::detail::executor_function&&, std::enable_if<boost::asio::can_query<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const, boost::asio::execution::allocator_t<void> >::value, void>::type*) boost/include/boost/asio/detail/impl/strand_executor_service.hpp:201  
    #14 0x5561b0b72ece in boost::asio::constraint<boost::asio::traits::execute_member<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const&, boost::asio::detail::executor_function, void>::is_valid, void>::type boost::asio::strand<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> >::execute<boost::asio::detail::executor_function>(boost::asio::detail::executor_function&&) const boost/include/boost/asio/strand.hpp:269  
    #15 0x5561b0b66916 in void boost::asio::execution::detail::any_executor_base::execute_ex<boost::asio::strand<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> > >(boost::asio::execution::detail::any_executor_base const&, boost::asio::detail::executor_function&&) boost/include/boost/asio/execution/any_executor.hpp:900  
    #16 0x5561b0b0e5f5 in void boost::asio::execution::detail::any_executor_base::execute<boost::asio::detail::binder0<boost::asio::detail::prepend_handler<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::system::error_code, unsigned long> > >(boost::asio::detail::binder0<boost::asio::detail::prepend_handler<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::system::error_code, unsigned long> >&&) const boost/include/boost/asio/execution/any_executor.hpp:681  
    #17 0x5561b0b00303 in boost::asio::detail::work_dispatcher<boost::asio::detail::prepend_handler<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::system::error_code, unsigned long>, boost::asio::any_io_executor, void>::operator()() boost/include/boost/asio/detail/work_dispatcher.hpp:81  
    #18 0x5561b0b008d4 in void boost::asio::detail::executor_function::complete<boost::asio::detail::work_dispatcher<boost::asio::detail::prepend_handler<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::system::error_code, unsigned long>, boost::asio::any_io_executor, void>, std::allocator<void> >(boost::asio::detail::executor_function::impl_base*, bool) boost/include/boost/asio/detail/executor_function.hpp:113  
    #19 0x5561b09bfefe in boost::asio::detail::executor_function::operator()() boost/include/boost/asio/detail/executor_function.hpp:61  
    #20 0x5561b0aabfd7 in boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) boost/include/boost/asio/detail/executor_op.hpp:70  
    #21 0x5561b09c61dc in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) boost/include/boost/asio/detail/scheduler_operation.hpp:40  
    #22 0x5561b09d37a7 in boost::asio::detail::strand_executor_service::run_ready_handlers(std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl>&) boost/include/boost/asio/detail/impl/strand_executor_service.ipp:150  
    #23 0x5561b0b7316e in boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul> const, void>::operator()() boost/include/boost/asio/detail/impl/strand_executor_service.hpp:121  
    #24 0x5561b0bc83ac in boost::asio::detail::executor_op<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul> const, void>, boost::asio::detail::recycling_allocator<void, boost::asio::detail::thread_info_base::default_tag>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) boost/include/boost/asio/detail/executor_op.hpp:70  
    #25 0x5561b09c61dc in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) boost/include/boost/asio/detail/scheduler_operation.hpp:40  
    #26 0x5561b09d1fcf in boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) boost/include/boost/asio/detail/impl/scheduler.ipp:493  
    #27 0x5561b09d1193 in boost::asio::detail::scheduler::run(boost::system::error_code&) boost/include/boost/asio/detail/impl/scheduler.ipp:210  
    #28 0x5561b09d945d in boost::asio::io_context::run() boost/include/boost/asio/impl/io_context.ipp:64  
    #29 0x5561b09bc0df in main ws_server.cpp:235  
```  
  
It's possible to reproduce it with the [official example](https://www.boost.org/doc/libs/1_85_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp) with just one change - enabling compression for the server.  
  
I believe it's a good idea to fix this in the library instead of catching the exception on the user side. I used a similar MR as a base for my one - https://github.com/boostorg/beast/pull/558 . This is my first MR for this repository so I would appreciate your feedback.

---

## Comment 1

> Username: ashtum  
> Created_at: 2024-06-02 20:25:58 UTC  
> Updated_at: 2024-06-02 20:28:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2144010480  

I believe the exception thrown by the allocator doesn't have the same meaning as `std::length_error`. You can use the [basic_flat_buffer::max_size](https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__basic_flat_buffer/max_size.html) interface to set the maximum allowed capacity.  
If you are trying to read a single message that doesn't fit into memory, you can use the [websocket::stream::async_read_some](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read_some.html) interface to read it chunk by chunk with specified limits.

---

## Comment 2

> Username: tyler92  
> Created_at: 2024-06-02 20:36:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2144013312  

Well, I followed the same logic as in the thread https://github.com/boostorg/beast/issues/553 where such a decision (replacing `std::length_error` with `std::exception`) was made. Am I right that you suggest making changes on the user side of the library rather than in the library itself?

---

## Comment 3

> Username: ashtum  
> Created_at: 2024-06-02 21:07:10 UTC  
> Updated_at: 2024-06-02 21:09:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2144021204  

> Well, I followed the same logic as in the thread #553 where such a decision (replacing `std::length_error` with `std::exception`) was made.  
  
That commits points to the old versions of the code which is replaced by: https://github.com/boostorg/beast/commit/c7a7d16992e30d60e50634b55ca8b524ba2d971c  
  
> Am I right that you suggest making changes on the user side of the library rather than in the library itself?  
  
Not really, I mean suppose you convert that allocation exception to a form of error_code, then what can you do with that error_code in the handler, retry again? If you want to set an upper limit to the maximum size, there is already an interface for it. If you want to read a very large message without allocating too much memory, it is doable by using the `async_read_some` interface.  
  
```  
==1739459==ERROR: AddressSanitizer: requested allocation size 0x4000000ffff073e (0x4000000ffff1740 after adjustments for alignment, red zones etc.) exceeds maximum supported size of 0x10000000000 (thread T0)  
```  
This exception complains about an allocation with a size of `0x4000000ffff1740` which doesn't seem right, could you please provide a minimal reproducible example of your code?

---

## Comment 4

> Username: tyler92  
> Created_at: 2024-06-02 21:16:50 UTC  
> Updated_at: 2024-06-02 21:21:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2144024068  

> suppose you convert that allocation exception to a form of error_code, then what can you do with that error_code in the handler, retry again?  
  
I think just log the error message, as probably for the case with `length_error`.  
  
> This exception complains about an allocation with a size of 0x4000000ffff1740 which doesn't seem right, could you please provide a minimal reproducible example of your code?  
  
Sure, as I already said it's [this example](https://www.boost.org/doc/libs/1_85_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp) + `permessage_deflate` option:  
  
<details>  
  <summary>ws_server.cpp</summary>  
    
  ```cpp  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
namespace beast = boost::beast;          // from <boost/beast.hpp>  
namespace http = beast::http;            // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket;  // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;             // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;        // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class session : public std::enable_shared_from_this<session>  
{  
    websocket::stream<beast::tcp_stream> ws_;  
    beast::flat_buffer buffer_;  
  
public:  
    // Take ownership of the socket  
    explicit session(tcp::socket&& socket) : ws_(std::move(socket))  
    {  
    }  
  
    // Get on the correct executor  
    void run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(  
            ws_.get_executor(),  
            beast::bind_front_handler(&session::on_run, shared_from_this()));  
    }  
  
    // Start the asynchronous operation  
    void on_run()  
    {  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(websocket::stream_base::timeout::suggested(  
            beast::role_type::server));  
  
        // Set a decorator to change the Server of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
            [](websocket::response_type& res) {  
                res.set(  
                    http::field::server,  
                    std::string(BOOST_BEAST_VERSION_STRING)  
                        + " websocket-server-async");  
            }));  
        ws_.set_option(websocket::permessage_deflate{  
            .server_enable = true,  
        });  
        // Accept the websocket handshake  
        ws_.async_accept(  
            beast::bind_front_handler(&session::on_accept, shared_from_this()));  
    }  
  
    void on_accept(beast::error_code ec)  
    {  
        if (ec)  
            return fail(ec, "accept");  
  
        // Read a message  
        do_read();  
    }  
  
    void do_read()  
    {  
        // Read a message into our buffer  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(&session::on_read, shared_from_this()));  
    }  
  
    void on_read(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the session was closed  
        if (ec == websocket::error::closed)  
            return;  
  
        if (ec)  
            fail(ec, "read");  
  
        // Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            buffer_.data(),  
            beast::bind_front_handler(&session::on_write, shared_from_this()));  
    }  
  
    void on_write(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if (ec)  
            return fail(ec, "write");  
  
        // Clear the buffer  
        buffer_.consume(buffer_.size());  
  
        // Do another read  
        do_read();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
    net::io_context& ioc_;  
    tcp::acceptor acceptor_;  
  
public:  
    listener(net::io_context& ioc, tcp::endpoint endpoint)  
        : ioc_(ioc)  
        , acceptor_(ioc)  
    {  
        beast::error_code ec;  
  
        // Open the acceptor  
        acceptor_.open(endpoint.protocol(), ec);  
        if (ec)  
        {  
            fail(ec, "open");  
            return;  
        }  
  
        // Allow address reuse  
        acceptor_.set_option(net::socket_base::reuse_address(true), ec);  
        if (ec)  
        {  
            fail(ec, "set_option");  
            return;  
        }  
  
        // Bind to the server address  
        acceptor_.bind(endpoint, ec);  
        if (ec)  
        {  
            fail(ec, "bind");  
            return;  
        }  
  
        // Start listening for connections  
        acceptor_.listen(net::socket_base::max_listen_connections, ec);  
        if (ec)  
        {  
            fail(ec, "listen");  
            return;  
        }  
    }  
  
    // Start accepting incoming connections  
    void run()  
    {  
        do_accept();  
    }  
  
private:  
    void do_accept()  
    {  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(&listener::on_accept, shared_from_this()));  
    }  
  
    void on_accept(beast::error_code ec, tcp::socket socket)  
    {  
        if (ec)  
        {  
            fail(ec, "accept");  
        }  
        else  
        {  
            // Create the session and run it  
            std::make_shared<session>(std::move(socket))->run();  
        }  
  
        // Accept another connection  
        do_accept();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
    // Check command line arguments.  
    if (argc != 4)  
    {  
        std::cerr << "Usage: websocket-server-async <address> <port> "  
                     "<threads>\n"  
                  << "Example:\n"  
                  << "    websocket-server-async 0.0.0.0 8080 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = net::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const threads = std::max<int>(1, std::atoi(argv[3]));  
  
    // The io_context is required for all I/O  
    net::io_context ioc{threads};  
  
    // Create and launch a listening port  
    std::make_shared<listener>(ioc, tcp::endpoint{address, port})->run();  
  
    // Run the I/O service on the requested number of threads  
    std::vector<std::thread> v;  
    v.reserve(threads - 1);  
    for (auto i = threads - 1; i > 0; --i)  
        v.emplace_back([&ioc] { ioc.run(); });  
  
    ioc.run();  
    return EXIT_SUCCESS;  
}  
  ```  
    
</details>  
  
And a malformed input message that I got during fuzzing:  
  
[crash-d22d762b7748edd0ac549ce67f8907a05c0eba4f.txt](https://github.com/user-attachments/files/15526892/crash-d22d762b7748edd0ac549ce67f8907a05c0eba4f.txt)  
  
How to reproduce:  
  
1. Run the server: `ws_server 0.0.0.0 9999 1`  
2. Run `cat crash-d22d762b7748edd0ac549ce67f8907a05c0eba4f.txt | nc  127.0.0.1 9999`

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2024-06-02 21:22:31 UTC  
> Updated_at: 2024-06-04 05:57:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2144025368  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2879?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.23%. Comparing base [(`146de67`)](https://app.codecov.io/gh/boostorg/beast/commit/146de67f5e9238e6159a6de7e20e8722df38a581?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`118c6e2`)](https://app.codecov.io/gh/boostorg/beast/commit/118c6e203231f034889998243e3aee5835073d57?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2879/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2879?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2879   +/-   ##  
========================================  
  Coverage    93.23%   93.23%             
========================================  
  Files          177      177             
  Lines        13670    13670             
========================================  
  Hits         12745    12745             
  Misses         925      925             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2879?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/buffer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2879?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fdetail%2Fbuffer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9idWZmZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2879?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2879?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [146de67...118c6e2](https://app.codecov.io/gh/boostorg/beast/pull/2879?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: ashtum  
> Created_at: 2024-06-03 10:50:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2144885577  

> And a malformed input message that I got during fuzzing:  
  
When `permessage-deflate` is disabled on the server, the response to that handshake request is:  
  
```  
HTTP/1.1 101 Switching Protocols  
Upgrade: websocket  
Connection: Upgrade  
Sec-WebSocket-Accept: Kfh9QIsMVZcl6xEPYxPHzW8SZ8w=  
Server: Boost.Beast/354 websocket-server-async  
```  
  
This means the client shouldn't use compression on frames. However, the file `crash-d22d762b7748edd0ac549ce67f8907a05c0eba4f.txt` contains a compressed message which gets rejected by the server with the error: `The WebSocket frame contained illegal reserved bits`. Consequently, the server doesn't attempt to read the rest of the message and doesn't need to prepare a buffer of that size. This explains why you don't get an allocation exception when `permessage-deflate` is disabled.

---

## Comment 7

> Username: tyler92  
> Created_at: 2024-06-03 12:23:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145067200  

This is clear. But the question - is it expected that ws_server.cpp crashes for this input? Should it be fixed in the Boost.Beast library or I need to adjust the basic_flat_buffer::max_size / catch the exception?

---

## Comment 8

> Username: ashtum  
> Created_at: 2024-06-03 13:35:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145224910  

> This is clear. But the question - is it expected that ws_server.cpp crashes for this input? Should it be fixed in the Boost.Beast library or I need to adjust the basic_flat_buffer::max_size / catch the exception?  
  
A real-life server should set an upper limit on the maximum message size, for example:  
```C++  
ws.read_message_max(64 * 1024 * 1024);  
```  
Alternatively, you can pass a limit for each call to `ws.async_read`. Otherwise, the only protection is the max_size of the buffer. This approach is similar to what Asio does in `read_until`:  
https://github.com/chriskohlhoff/asio/blob/12e0ce9e0500bf0f247dbd1ae894272656456079/asio/include/asio/impl/read_until.hpp#L1131

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2024-06-03 18:00:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145810964  

When the server receives a message from a client with invalid bits in the message frame header, Beast should not throw an exception. Instead, it should signal the invalid condition with an `error_code`, and the server should close the connection with an appropriate message written to the log.

---

## Comment 10

> Username: tyler92  
> Created_at: 2024-06-03 18:12:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145832362  

@vinniefalco Yes, I agree with you, that's why I decided to try to fix this exception. Do I understand correctly that my intention to correct the current behavior makes sense? If so, do you agree with how I fixed it, or is there a better solution?  
  
Regardless of the answer, I want to express my opinion about the quality of the library code - it is very high!

---

## Comment 11

> Username: ashtum  
> Created_at: 2024-06-03 18:17:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145840929  

> When the server receives a message from a client with invalid bits in the message frame header, Beast should not throw an exception. Instead, it should signal the invalid condition with an `error_code`, and the server should close the connection with an appropriate message written to the log.  
  
It currently doesn't throw an exception; instead, it completes with `error::bad_reserved_bits` in this case. The discussed exception occurs when allocation in `buffer.prepare(size)` throws.

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2024-06-03 18:20:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145844971  

> do you agree with how I fixed it  
  
Definitely not :) The correct way to fix this is to first check for overflow in the dynamic buffer, in the read_op, and return an error_code if there is not enough space. The maximum space can be determined by looking at `max_size() - size()`. This check would go here  
  
https://github.com/boostorg/beast/blob/4bff457ef7064c3288905d23bc728b70acc7ede2/include/boost/beast/websocket/impl/read.hpp#L791  
  
and possibly other places, @ashtum can you please weigh in?

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2024-06-03 18:21:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145846505  

> ...it completes with error::bad_reserved_bits  
  
Why isn't the server receiving this error before an attempt is made to read the frame payload?

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2024-06-03 18:27:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145857572  

@ashtum we must add two minimal tests:  
  
1. Client sending invalid frame header bits to the server  
2. Attempted read into dynamic buffer with insufficient maximum size

---

## Comment 15

> Username: ashtum  
> Created_at: 2024-06-03 18:38:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145878797  

> The correct way to fix this is to first check for overflow in the dynamic buffer, in the read_op, and return an error_code if there is not enough space. The maximum space can be determined by looking at `max_size() - size()`. This check would go here  
> and possibly other places, @ashtum can you please weigh in?  
  
I believe It already does that inside the beast::detail::dynamic_buffer_prepare:  
https://github.com/boostorg/beast/blob/4bff457ef7064c3288905d23bc728b70acc7ede2/include/boost/beast/core/detail/buffer.hpp#L70  
  
> Why isn't the server receiving this error before an attempt is made to read the frame payload?  
  
The file `crash-d22d762b7748edd0ac549ce67f8907a05c0eba4f.txt` contains a handshake request and a valid frame. When `permessage-deflate` is disabled on the server, it responds to the client to not compress the frame. An attempt to read it finds it as an unexpected frame and completes with `error::bad_reserved_bits`.  
However, when `permessage-deflate` is enabled, the frame is legitimate but has a huge size. Without setting any limitation on the buffer or WebSocket stream, it calls `buffer.prepare` with that size, which throws a `bad_alloc`.

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2024-06-03 18:45:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145891584  

> the frame is legitimate but has a huge size  
  
When Beast is called upon to read a huge frame into a dynamic buffer, it is our responsibility to check that the frame can fit within the maximum of that dynamic buffer. If it cannot, we must return an `error_code` to the caller not throw. More generally, whenever Beast handles a user-provided dynamic buffer as part of an I/O operation it must never invoke the dynamic buffer in a way that would throw, unless the exception comes from the standard library directly or indirectly such as through `malloc` or `new`.  
  
In this case I believe that the exception is coming from the attempt to allocate via std? If yes, then the exception is correct and the proper solution is for the caller to set a lower maximum size on the dynamic buffer or to set a maximum message size for websocket. Do we already have default maximum websocket message sizes for clients and servers?

---

## Comment 17

> Username: tyler92  
> Created_at: 2024-06-03 18:52:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145902187  

> In this case I believe that the exception is coming from the attempt to allocate via std?   
  
Yes, there is a call stack in the first message.

---

## Comment 18

> Username: tyler92  
> Created_at: 2024-06-03 19:09:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145930088  

> If yes, then the exception is correct and the proper solution is for the caller to set a lower maximum size on the dynamic buffer or to set a maximum message size for websocket.   
  
Thank you for the answer. I guess I can close this MR

---

## Comment 19

> Username: ashtum  
> Created_at: 2024-06-03 19:10:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2145931520  

> When Beast is called upon to read a huge frame into a dynamic buffer, it is our responsibility to check that the frame can fit within the maximum of that dynamic buffer. If it cannot, we must return an error_code to the caller not throw. More generally, whenever Beast handles a user-provided dynamic buffer as part of an I/O operation it must never invoke the dynamic buffer in a way that would throw, unless the exception comes from the standard library directly or indirectly such as through malloc or new.  
  
Yes, this is what the current implementation does.  
  
> Do we already have default maximum websocket message sizes for clients and servers?  
  
Interesting, it is 16MB by default. I had assumed it wasn't set by default, so the behavior seemed expected. It appears we only check it when message is not deflated:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/stream_impl.hpp#L889-L892  
  
It is checked in a few other places in the read operation too, which I need to verify. But this is definitely a bug.

---

## Comment 20

> Username: vinniefalco  
> Created_at: 2024-06-03 20:11:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2146034297  

If the message is deflated we should check for the limit twice: one, to make sure that the deflated message is not over the limit, and then again to make sure that the decompressed message is not over the limit.

---

## Comment 21

> Username: ashtum  
> Created_at: 2024-06-03 20:42:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2146086068  

> If the message is deflated we should check for the limit twice: one, to make sure that the deflated message is not over the limit, and then again to make sure that the decompressed message is not over the limit.  
  
I believe what is happening is that in the call to `parse_fh`, we don't know the final size of the message after inflation, so we don't check for `rd_msg_max`, hoping that later, when we deflate frames, we will hit the `rd_msg_max`. After the first call to `async_read_some`, we call `read_size_hint_pmd` to get a suggestion for preparing the user-provided buffer, which is indeed the value of `rd_remain`.  
  
https://github.com/boostorg/beast/blob/4bff457ef7064c3288905d23bc728b70acc7ede2/include/boost/beast/websocket/impl/read.hpp#L791-L793  
  
  
Note that the `limit_` in the linked line is not `rd_msg_max`; it is the optional limit parameter passed to `ws.async_read`.

---

## Comment 22

> Username: ashtum  
> Created_at: 2024-06-03 21:03:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2146118167  

This could have happened for an uncompressed message too, but it is prevented earlier in `parse_fh`:  
https://github.com/boostorg/beast/blob/4bff457ef7064c3288905d23bc728b70acc7ede2/include/boost/beast/websocket/impl/stream_impl.hpp#L889-L898

---

## Comment 23

> Username: ashtum  
> Created_at: 2024-06-06 20:49:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2879#issuecomment-2153384367  

@tyler92, thanks for reporting the bug. The fix is already in the master branch and will be included in the upcoming Boost release.

---
