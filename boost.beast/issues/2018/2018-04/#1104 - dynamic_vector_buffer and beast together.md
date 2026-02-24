# #1104 - dynamic_vector_buffer and beast together [Closed]

> Username: shuras109  
> Created at: 2018-04-25 16:36:27 UTC  
> Updated at: 2018-06-03 02:55:32 UTC  
> Closed at: 2018-06-03 02:55:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1104  

Hi there!  
Possibly it is not an issue and should be closed. My knowledge for a buffer usage in boost and beast is quite limited. So maybe I do something terribly wrong. Or there is a problem with code of some sorts.   
  
Let me to begin from afar.  
I am looking for a way to create multi-threaded program with one thread receiving from websocket pieces of data, store it and another thread process these blocks of data later.  
  
To do so I was looking for a way to avoid excessive work with a copying of received data blocks.  
As I understand it is not possible to achieve with a usage of flat_buffer or similar buffers, because buffer memory is kept inside of the buffer itself. If you want to get a block of data you should copy and then consume copied block from the buffer. Is it right?   
  
So I found info about dynamic_vector_buffer and looks like it is possible to associate std::vector with it as a memory storage.  
  
So I took an example of async_server and modify it in a way to use dynamic_vector_buffer during ws read. As result compiled program is working, but both address_sanitizer and valgrind raise red flags about potential stack overflow. But not always. On my Ubuntu stable this problem present all the time and on debian it can work flawlessly but if you tweak program a little bit it pop up in the same place.   
  
Is it my wrong usage of dynamic_vector_buffer or is it a bug in the library?  
  
Below is modified sample:  
```  
//  
// Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: WebSocket server, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(boost::system::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class session : public std::enable_shared_from_this<session>  
{  
    websocket::stream<tcp::socket> ws_;  
    boost::asio::strand<  
        boost::asio::io_context::executor_type> strand_;  
    boost::beast::multi_buffer buffer_;  
    std::vector<char, std::allocator<char>> test_buf_;  
  
public:  
    // Take ownership of the socket  
    explicit  
    session(tcp::socket socket)  
        : ws_(std::move(socket))  
        , strand_(ws_.get_executor())  
    {  
    }  
  
    // Start the asynchronous operation  
    void  
    run()  
    {  
        // Accept the websocket handshake  
        ws_.async_accept(  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &session::on_accept,  
                    shared_from_this(),  
                    std::placeholders::_1)));  
    }  
  
    void  
    on_accept(boost::system::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "accept");  
  
        // Read a message  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        test_buf_.resize(1);  
        boost::asio::dynamic_vector_buffer<char, std::allocator<char>> buf(test_buf_);  
  
        // Read a message into our buffer  
        ws_.async_read(  
            buf,  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &session::on_read,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2)));  
    }  
  
    void  
    on_read(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the session was closed  
        if(ec == websocket::error::closed)  
            return;  
  
        if(ec)  
            fail(ec, "read");  
  
        // Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            boost::asio::buffer(test_buf_.data(), test_buf_.size()),  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &session::on_write,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2)));  
    }  
  
    void  
    on_write(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
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
    tcp::acceptor acceptor_;  
    tcp::socket socket_;  
  
public:  
    listener(  
        boost::asio::io_context& ioc,  
        tcp::endpoint endpoint)  
        : acceptor_(ioc)  
        , socket_(ioc)  
    {  
        boost::system::error_code ec;  
  
        // Open the acceptor  
        acceptor_.open(endpoint.protocol(), ec);  
        if(ec)  
        {  
            fail(ec, "open");  
            return;  
        }  
  
        // Allow address reuse  
        acceptor_.set_option(boost::asio::socket_base::reuse_address(true));  
        if(ec)  
        {  
            fail(ec, "set_option");  
            return;  
        }  
  
        // Bind to the server address  
        acceptor_.bind(endpoint, ec);  
        if(ec)  
        {  
            fail(ec, "bind");  
            return;  
        }  
  
        // Start listening for connections  
        acceptor_.listen(  
            boost::asio::socket_base::max_listen_connections, ec);  
        if(ec)  
        {  
            fail(ec, "listen");  
            return;  
        }  
    }  
  
    // Start accepting incoming connections  
    void  
    run()  
    {  
        if(! acceptor_.is_open())  
            return;  
        do_accept();  
    }  
  
    void  
    do_accept()  
    {  
        acceptor_.async_accept(  
            socket_,  
            std::bind(  
                &listener::on_accept,  
                shared_from_this(),  
                std::placeholders::_1));  
    }  
  
    void  
    on_accept(boost::system::error_code ec)  
    {  
        if(ec)  
        {  
            fail(ec, "accept");  
        }  
        else  
        {  
            // Create the session and run it  
            std::make_shared<session>(std::move(socket_))->run();  
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
        std::cerr <<  
            "Usage: websocket-server-async <address> <port> <threads>\n" <<  
            "Example:\n" <<  
            "    websocket-server-async 0.0.0.0 8080 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = boost::asio::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const threads = std::max<int>(1, std::atoi(argv[3]));  
  
    // The io_context is required for all I/O  
    boost::asio::io_context ioc{threads};  
  
    // Create and launch a listening port  
    std::make_shared<listener>(ioc, tcp::endpoint{address, port})->run();  
  
    // Run the I/O service on the requested number of threads  
    std::vector<std::thread> v;  
    v.reserve(threads - 1);  
    for(auto i = threads - 1; i > 0; --i)  
        v.emplace_back(  
        [&ioc]  
        {  
            ioc.run();  
        });  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}  
  
```  
  
And here is sanitaizer output:  
```  
==31005==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7fff0c4490d0 at pc 0x000000468776 bp 0x7fff0c448eb0 sp 0x7fff0c448ea0  
READ of size 8 at 0x7fff0c4490d0 thread T0  
    #0 0x468775 in boost::asio::dynamic_vector_buffer<char, std::allocator<char> >::commit(unsigned long) /usr/local/boost/include/boost/asio/buffer.hpp:1807  
    #1 0x453cef in boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >::operator()(boost::system::error_code, unsigned long) /usr/local/boost/include/boost/beast/websocket/impl/read.ipp:828  
    #2 0x46855e in boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >::operator()(boost::system::error_code, unsigned long, bool) /usr/local/boost/include/boost/beast/websocket/impl/read.ipp:707  
    #3 0x50c161 in boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>::operator()() /usr/local/boost/include/boost/asio/detail/bind_handler.hpp:164  
    #4 0x5007a4 in void boost::asio::asio_handler_invoke<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long> >(boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&, ...) /usr/local/boost/include/boost/asio/handler_invoke_hook.hpp:69  
    #5 0x4f7ff4 in void boost::beast::websocket::asio_handler_invoke<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&>(boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >*) /usr/local/boost/include/boost/beast/websocket/impl/read.ipp:783  
    #6 0x4eddb5 in void boost::beast::websocket::asio_handler_invoke<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&>(boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >*) /usr/local/boost/include/boost/beast/websocket/impl/read.ipp:155  
    #7 0x4e3b9f in void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > > >(boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >&) /usr/local/boost/include/boost/asio/detail/handler_invoke_helpers.hpp:37  
    #8 0x4d80aa in void boost::asio::detail::asio_handler_invoke<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>(boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>*) /usr/local/boost/include/boost/asio/detail/bind_handler.hpp:207  
    #9 0x4cfc83 in void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>, boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long> >(boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&) /usr/local/boost/include/boost/asio/detail/handler_invoke_helpers.hpp:37  
    #10 0x4d8530 in boost::asio::detail::executor_op<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) /usr/local/boost/include/boost/asio/detail/executor_op.hpp:70  
    #11 0x40bb12 in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) /usr/local/boost/include/boost/asio/detail/scheduler_operation.hpp:40  
    #12 0x4edf30 in boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>::operator()() /usr/local/boost/include/boost/asio/detail/impl/strand_executor_service.hpp:90  
    #13 0x4e3d47 in void boost::asio::asio_handler_invoke<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const> >(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, ...) /usr/local/boost/include/boost/asio/handler_invoke_hook.hpp:69  
    #14 0x4d87bf in void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>, boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const> >(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&) /usr/local/boost/include/boost/asio/detail/handler_invoke_helpers.hpp:37  
    #15 0x4d003f in void boost::asio::io_context::executor_type::dispatch<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>, std::allocator<void> >(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&&, std::allocator<void> const&) const /usr/local/boost/include/boost/asio/impl/io_context.hpp:243  
    #16 0x4c3beb in void boost::asio::detail::strand_executor_service::dispatch<boost::asio::io_context::executor_type const, boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>, std::allocator<void> >(std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::executor_type const&, boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&&, std::allocator<void> const&) /usr/local/boost/include/boost/asio/detail/impl/strand_executor_service.hpp:128  
    #17 0x4b50bd in void boost::asio::strand<boost::asio::io_context::executor_type>::dispatch<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>, std::allocator<void> >(boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&&, std::allocator<void> const&) const /usr/local/boost/include/boost/asio/strand.hpp:193  
    #18 0x4aa734 in void boost::asio::detail::handler_work<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::asio::strand<boost::asio::io_context::executor_type> >::complete<boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long> >(boost::asio::detail::binder2<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >, boost::system::error_code, unsigned long>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > >&) /usr/local/boost/include/boost/asio/detail/handler_work.hpp:55  
    #19 0x49f0cf in boost::asio::detail::reactive_socket_recv_op<std::array<boost::asio::mutable_buffer, 2ul>, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffers_1, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > > > >::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) /usr/local/boost/include/boost/asio/detail/reactive_socket_recv_op.hpp:122  
    #20 0x40bb12 in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) /usr/local/boost/include/boost/asio/detail/scheduler_operation.hpp:40  
    #21 0x413878 in boost::asio::detail::epoll_reactor::descriptor_state::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) /usr/local/boost/include/boost/asio/detail/impl/epoll_reactor.ipp:776  
    #22 0x40bb12 in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) /usr/local/boost/include/boost/asio/detail/scheduler_operation.hpp:40  
    #23 0x415412 in boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) /usr/local/boost/include/boost/asio/detail/impl/scheduler.ipp:401  
    #24 0x414769 in boost::asio::detail::scheduler::run(boost::system::error_code&) /usr/local/boost/include/boost/asio/detail/impl/scheduler.ipp:154  
    #25 0x415ec5 in boost::asio::io_context::run() /usr/local/boost/include/boost/asio/impl/io_context.ipp:62  
    #26 0x405ec5 in main /home/shuras/tmp/4/testbuf/server.cpp:275  
    #27 0x7f625336482f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)  
    #28 0x4056b8 in _start (/home/shuras/tmp/4/testbuf/server+0x4056b8)  
  
Address 0x7fff0c4490d0 is located in stack of thread T0 at offset 64 in frame  
    #0 0x453789 in boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::asio::dynamic_vector_buffer<char, std::allocator<char> >, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, unsigned long)> (std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >::operator()(boost::system::error_code, unsigned long) /usr/local/boost/include/boost/beast/websocket/impl/read.ipp:790  
  
  This frame has 2 object(s):  
    [32, 48) '_coro_value' <== Memory access at offset 64 overflows this variable  
    [96, 120) 'mb'  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism or swapcontext  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-buffer-overflow /usr/local/boost/include/boost/asio/buffer.hpp:1807 boost::asio::dynamic_vector_buffer<char, std::allocator<char> >::commit(unsigned long)  
Shadow bytes around the buggy address:  
  0x1000618811c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1000618811d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1000618811e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1000618811f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100061881200: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x100061881210: 00 00 f1 f1 f1 f1 00 00 f4 f4[f2]f2 f2 f2 00 00  
  0x100061881220: 00 f4 f3 f3 f3 f3 00 00 00 00 00 00 00 00 00 00  
  0x100061881230: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100061881240: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100061881250: f1 f1 f1 f1 00 00 f4 f4 f2 f2 f2 f2 00 00 f4 f4  
  0x100061881260: f2 f2 f2 f2 00 00 00 00 00 00 00 f4 f2 f2 f2 f2  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Heap right redzone:      fb  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack partial redzone:   f4  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
==31005==ABORTING  
  
```

---

## Comment 1

> Username: shuras109  
> Created at: 2018-04-25 16:38:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1104#issuecomment-384352525  

Forget to mention: I am using 1.67 version of boost with included beast.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-25 17:48:35 UTC  
> Updated at: 2018-04-25 17:49:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1104#issuecomment-384375085  

This code is incorrect:  
```  
        boost::asio::dynamic_vector_buffer<char, std::allocator<char>> buf(test_buf_);  
  
        // Read a message into our buffer  
        ws_.async_read(  
            buf,  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &session::on_read,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2)));  
```  
  
You are passing a non-const reference to a stack variable to `async_read`. The documentation states the requirements:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html  
  
`buffer`: A dynamic buffer to hold the message data after any masking or decompression has been applied. This object must remain valid until the handler is called.  
  
You can instead make the `dynamic_vector_buffer` a member of your class. You will be responsible for constructing it and destroying it, so I suggest you put it in a `boost::optional` or `std::optional`, since the underlying `vector` will not have the correct length until the `dynamic_vector_buffer` object is destroyed.

---

## Comment 3

> Username: shuras109  
> Created at: 2018-04-25 21:39:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1104#issuecomment-384442965  

Thank you very much Vinnie. Now it is slowly starting to make sense to me. All this different types of buffers in Boost and Beast is quite confusing at first. All I need is a little push in right direction. And your guidance is invaluable.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-05-04 02:30:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1104#issuecomment-386491621  

This will be addressed in a better way shortly

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-06-03 02:34:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1104#issuecomment-394130568  

This issue has been open for a while with no activity, has it been resolved?
