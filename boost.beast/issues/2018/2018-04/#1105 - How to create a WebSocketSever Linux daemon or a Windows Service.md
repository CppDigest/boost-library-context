# #1105 - How to create a WebSocketSever Linux daemon or a Windows Service [Closed]

> Username: bangusi  
> Created at: 2018-04-26 00:13:50 UTC  
> Updated at: 2018-04-28 07:48:26 UTC  
> Closed at: 2018-04-28 07:48:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1105  

I need to convert the example server code into a Linux daemon and Windows service.  
  
### Version of Beast  
Boost / beast Version 1.66.0  
### Steps necessary to reproduce the problem  
Using example code in **websocket_server_sync.cpp**  
  
### All relevant compiler information  
Windows 10  
Visual Studio 2017  
x64,  
Static CRT linkage  
`#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <string>  
#include <thread>  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
//------------------------------------------------------------------------------  
  
// Echoes back all received WebSocket messages  
void  
do_session(tcp::socket& socket)  
{  
    try  
    {  
        // Construct the stream by moving in the socket  
        websocket::stream<tcp::socket> ws{std::move(socket)};  
  
        // Accept the websocket handshake  
        ws.accept();  
  
        for(;;)  
        {  
            // This buffer will hold the incoming message  
            boost::beast::multi_buffer buffer;  
  
            // Read a message  
            ws.read(buffer);  
  
            // Echo the message back  
            ws.text(ws.got_text());  
            ws.write(buffer.data());  
        }  
    }  
    catch(boost::system::system_error const& se)  
    {  
        // This indicates that the session was closed  
        if(se.code() != websocket::error::closed)  
            std::cerr << "Error: " << se.code().message() << std::endl;  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
    }  
}  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
    try  
    {  
        // Check command line arguments.  
        if (argc != 3)  
        {  
            std::cerr <<  
                "Usage: websocket-server-sync <address> <port>\n" <<  
                "Example:\n" <<  
                "    websocket-server-sync 0.0.0.0 8080\n";  
            return EXIT_FAILURE;  
        }  
        auto const address = boost::asio::ip::make_address(argv[1]);  
        auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc{1};  
  
        // The acceptor receives incoming connections  
        tcp::acceptor acceptor{ioc, {address, port}};  
        for(;;)  
        {  
            // This will receive the new connection  
            tcp::socket socket{ioc};  
  
            // Block until we get a connection  
            acceptor.accept(socket);  
  
            // Launch the session, transferring ownership of the socket  
            std::thread{std::bind(  
                &do_session,  
                std::move(socket))}.detach();  
        }  
    }  
    catch (const std::exception& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
}`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-26 00:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1105#issuecomment-384472900  

That is outside the scope of Beast
