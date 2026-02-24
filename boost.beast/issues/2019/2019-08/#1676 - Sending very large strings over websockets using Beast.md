# #1676 - Sending very large strings over websockets using Beast [Closed]

> Username: 07sainishanth  
> Created at: 2019-08-12 16:24:29 UTC  
> Updated at: 2019-08-13 05:59:34 UTC  
> Closed at: 2019-08-13 05:59:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1676  

I am trying to send a very large string (strlen 50,000) over websockets using beast.  
`std::string ss1 = some_function();  
net::mutable_buffer  cb0(&ss1[0], size(s1));  
//size(s1) > - 45,000`  
  
I was not facing any issues if the size is less than 4096.  
I am facing an issue while converting string to buffer.   
  
accessing unallocated memory error arises when converting to buffer  
  
I traced the error back to _boost/asio/buffer.hpp_ file  
memcpy function (**line 2179**) ;- then to  
`template <typename TargetIterator, typename SourceIterator>  
std::size_t buffer_copy(one_buffer, multiple_buffers,  
    TargetIterator target_begin, TargetIterator,  
    SourceIterator source_begin, SourceIterator source_end,  
    std::size_t max_bytes_to_copy  
      = (std::numeric_limits<std::size_t>::max)()) ASIO_NOEXCEPTr` fun(**line 2201** )  
  
I am adding the whole sample code that I used  
`//  
// Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: WebSocket client, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <fstream>  
  
  
using namespace std;  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
//------------------------------------------------------------------------------  
  
std::string read_data(){  
	//read data from a file  
	string line;  
	string rline;  
  ifstream myfile ("examples.txt");  
  if (myfile.is_open())  
  {  
    while ( getline (myfile,line) )  
    {  
      cout << line << '\n';  
	  rline = rline + rline;  
    }  
    myfile.close();  
  }  
  
  else cout << "Unable to open file";   
    
  return rline;  
}  
  
// Report a failure  
void  
fail(boost::system::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Sends a WebSocket message and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::resolver resolver_;  
    websocket::stream<tcp::socket> ws_;  
    boost::beast::multi_buffer buffer_;  
    std::string host_;  
    std::string text_;  
  
public:  
    // Resolver and socket require an io_context  
    explicit  
    session(boost::asio::io_context& ioc)  
        : resolver_(ioc)  
        , ws_(ioc)  
    {  
    }  
  
    // Start the asynchronous operation  
    void  
    run(  
        char const* host,  
        char const* port,  
        char const* text)  
    {  
        // Save these for later  
        host_ = host;  
        text_ = text;  
  
        // Look up the domain name  
        resolver_.async_resolve(  
            host,  
            port,  
            std::bind(  
                &session::on_resolve,  
                shared_from_this(),  
                std::placeholders::_1,  
                std::placeholders::_2));  
    }  
  
    void  
    on_resolve(  
        boost::system::error_code ec,  
        tcp::resolver::results_type results)  
    {  
        if(ec)  
            return fail(ec, "resolve");  
  
        // Make the connection on the IP address we get from a lookup  
        boost::asio::async_connect(  
            ws_.next_layer(),  
            results.begin(),  
            results.end(),  
            std::bind(  
                &session::on_connect,  
                shared_from_this(),  
                std::placeholders::_1));  
    }  
  
    void  
    on_connect(boost::system::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "connect");  
  
        // Perform the websocket handshake  
        ws_.async_handshake(host_, "/",  
            std::bind(  
                &session::on_handshake,  
                shared_from_this(),  
                std::placeholders::_1));  
    }  
  
    void  
    on_handshake(boost::system::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
          
        // Send the message  
        ws_.async_write(  
            boost::asio::buffer(text_),  
            std::bind(  
                &session::on_write,  
                shared_from_this(),  
                std::placeholders::_1,  
                std::placeholders::_2));  
    }  
  
    void  
    on_write(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
          
        // Read a message into our buffer  
        ws_.async_read(  
            buffer_,  
            std::bind(  
                &session::on_read,  
                shared_from_this(),  
                std::placeholders::_1,  
                std::placeholders::_2));  
    }  
  
    void  
    on_read(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "read");  
		else{  
			std::string ss1 = read_data();  
			net::mutable_buffer cb0(&ss1[0], size(s1));  
			ws_.async_write(  
            boost::asio::buffer(ss1),  
            std::bind(  
                &session::on_write,  
                shared_from_this(),  
                std::placeholders::_1,  
                std::placeholders::_2));  
		}  
  
        // Close the WebSocket connection  
        ws_.async_close(websocket::close_code::normal,  
            std::bind(  
                &session::on_close,  
                shared_from_this(),  
                std::placeholders::_1));  
    }  
  
    void  
    on_close(boost::system::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "close");  
  
        // If we get here then the connection is closed gracefully  
  
        // The buffers() function helps print a ConstBufferSequence  
        std::cout << boost::beast::buffers(buffer_.data()) << std::endl;  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv)  
{  
    // Check command line arguments.  
    if(argc != 4)  
    {  
        std::cerr <<  
            "Usage: websocket-client-async <host> <port> <text>\n" <<  
            "Example:\n" <<  
            "    websocket-client-async echo.websocket.org 80 \"Hello, world!\"\n";  
        return EXIT_FAILURE;  
    }  
    auto const host = argv[1];  
    auto const port = argv[2];  
    auto const text = argv[3];  
  
    // The io_context is required for all I/O  
    boost::asio::io_context ioc;  
  
    // Launch the asynchronous operation  
    std::make_shared<session>(ioc)->run(host, port, text);  
  
    // Run the I/O service. The call will return when  
    // the socket is closed.  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}`  
  
And attached is the sample data file  
[examples.txt](https://github.com/boostorg/beast/files/3493865/examples.txt)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-12 16:42:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1676#issuecomment-520501703  

We need more information. Can you show the code that sends the string using the beast websocket?

---

## Comment 2

> Username: 07sainishanth  
> Created at: 2019-08-12 18:49:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1676#issuecomment-520549140  

>   
>   
> We need more information. Can you show the code that sends the string using the beast WebSocket?  
  
I have attached the code and data file I was Using, Ignore the compilation errors if any and could you reply on how to send very large data in one go.   
  
Thank you

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-08-12 19:42:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1676#issuecomment-520567071  

> `boost::asio::buffer(ss1)`  
  
`ss1` gets destroyed when it goes out of scope, leading to an access violation. You must ensure that the lifetime of the string extends until at least the invocation of the completion handler.

---

## Comment 4

> Username: 07sainishanth  
> Created at: 2019-08-13 05:59:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1676#issuecomment-520699819  

Thanks a lot .Its working
