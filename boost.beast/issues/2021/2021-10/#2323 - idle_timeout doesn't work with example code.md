# #2323 - idle_timeout doesn't work with example code [Closed]

> Username: Dalzhim  
> Created at: 2021-10-05 21:39:37 UTC  
> Updated at: 2021-10-06 16:36:40 UTC  
> Closed at: 2021-10-06 15:23:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2323  

The idle_timeout property of the timeout options doesn't seem to work as documented. I am using the [asynchronous websocket server](https://www.boost.org/doc/libs/1_77_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp) example code in which I have made one single change:  
```  
        // Set suggested timeout settings for the websocket  
//        ws_.set_option(  
//            websocket::stream_base::timeout::suggested(  
//                beast::role_type::server));  
        websocket::stream_base::timeout options = websocket::stream_base::timeout::suggested(beast::role_type::server);  
        options.idle_timeout = std::chrono::seconds{3};  
        ws_.set_option(options);  
```  
  
Then, I create a websocket connexion using my web browser's console by entering the following command: `$ws = new WebSocket("ws://127.0.0.1/");$ws.onmessage = console.log;$ws.onclose = console.log;`. The connection is successfully established, but the connection lives on, and I can send a message and receive the echo after more than thirty seconds of idling.  
  
I can reproduce the problem with multiple versions of boost : 1.73, 1.74, 1.75, 1.76 and 1.77.  
  
Below is the complete code for convenience. And here is a summary of the steps to reproduce:  
1. Compile the code (i.e. : `g++ main.cpp --std=c++17 -I$(pwd)/boost`)  
2. Run the program (i.e. : `./a.out 127.0.0.1 80 1`  
3. Open the inspector in your browser  
4. Open a WebSocket : `$ws = new WebSocket("ws://127.0.0.1/");$ws.onmessage = console.log;$ws.onclose = console.log;`  
5. Wait at least 3 seconds (current value of the timeout)  
6. WebSocket should be getting closed because of the timeout.  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/asio/strand.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
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
    explicit  
    session(tcp::socket&& socket)  
        : ws_(std::move(socket))  
    {  
    }  
  
    // Get on the correct executor  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(ws_.get_executor(),  
            beast::bind_front_handler(  
                &session::on_run,  
                shared_from_this()));  
    }  
  
    // Start the asynchronous operation  
    void  
    on_run()  
    {  
        // Set suggested timeout settings for the websocket  
        websocket::stream_base::timeout options = websocket::stream_base::timeout::suggested(beast::role_type::server);  
				options.idle_timeout = std::chrono::seconds{3};  
        ws_.set_option(options);  
  
        // Set a decorator to change the Server of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
            [](websocket::response_type& res)  
            {  
                res.set(http::field::server,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                        " websocket-server-async");  
            }));  
        // Accept the websocket handshake  
        ws_.async_accept(  
            beast::bind_front_handler(  
                &session::on_accept,  
                shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "accept");  
  
        // Read a message  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Read a message into our buffer  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
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
            buffer_.data(),  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
    }  
  
    void  
    on_write(  
        beast::error_code ec,  
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
    net::io_context& ioc_;  
    tcp::acceptor acceptor_;  
  
public:  
    listener(  
        net::io_context& ioc,  
        tcp::endpoint endpoint)  
        : ioc_(ioc)  
        , acceptor_(ioc)  
    {  
        beast::error_code ec;  
  
        // Open the acceptor  
        acceptor_.open(endpoint.protocol(), ec);  
        if(ec)  
        {  
            fail(ec, "open");  
            return;  
        }  
  
        // Allow address reuse  
        acceptor_.set_option(net::socket_base::reuse_address(true), ec);  
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
            net::socket_base::max_listen_connections, ec);  
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
        do_accept();  
    }  
  
private:  
    void  
    do_accept()  
    {  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(  
                &listener::on_accept,  
                shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec, tcp::socket socket)  
    {  
        if(ec)  
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
        std::cerr <<  
            "Usage: websocket-server-async <address> <port> <threads>\n" <<  
            "Example:\n" <<  
            "    websocket-server-async 0.0.0.0 8080 1\n";  
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

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-05 22:20:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2323#issuecomment-934936642  

Well yeah, its working as designed :) beast sends and responds to idle pings!! There is still activity taking place. If you don't want Beast to send idle pings you have to set `keep_alive_pings=false` in the timeout options structure. But even then, Beast will probably respond to incoming idle pings automatically, which will still prevent the timeout.  We might also need an option not to respond to such pings. But that kind of goes against the RFC.

---

## Comment 2

> Username: Dalzhim  
> Created at: 2021-10-05 22:45:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2323#issuecomment-934966889  

So if I understand correctly, the beast server will send pings on its own on a regular basis? I would have thought the client would be the one initiating pings. What happens when both ends of the communication use beast? Do both initiate pings?  
  
Also, maybe I am asking the wrong question. I am trying to make sure a LoadBalancer doesn't kill the socket based on inactivity by making sure the pings and pongs are sent, but I still want to have a timeout in case there are no messages being exchanged after a set amount of time. For example, the LoadBalancer might have a timeout of 60 seconds, but I want the socket to have a 600 seconds timeout. Isn't that what the idle_timeout is meant for?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-10-05 22:51:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2323#issuecomment-934974335  

> What happens when both ends of the communication use beast? Do both initiate pings?  
  
The default settings for client is to send the ping and I believe the default setting for the server is to wait for the ping:  
  
https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/stream_base.hpp#L144  
  
If pings and pongs are exchanged regularly, the websocket stream may never time out.

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-10-06 06:54:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2323#issuecomment-935601438  

It's the server that initiates pings by default in Beast. But yes, it is a feature of websockets that they keep themselves alive automatically in the general case.

---

## Comment 5

> Username: Dalzhim  
> Created at: 2021-10-06 15:23:44 UTC  
> Updated at: 2021-10-06 16:36:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2323#issuecomment-936491268  

Thank you for these clarifications, it is very helpful. May I suggest that they could enrich the documentation? As of now, the following excerpt is misleading. The problem is that the sample code suggests the timeouts will be enabled that way. But as this issue makes clear, the server side will enable the pings with the suggested options and it won't be possible to prove that the `idle_timeout` is working by just "idling".  
  
```  
By default, timeouts on websocket streams are disabled. The easiest way to turn them on is to set the suggested timeout settings on the stream.  
  
// Apply suggested timeout options for the server role to the stream  
ws.set_option(stream_base::timeout::suggested(role_type::server));  
```
