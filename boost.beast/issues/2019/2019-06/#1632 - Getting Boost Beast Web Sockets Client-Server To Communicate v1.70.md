# #1632 - Getting Boost Beast Web Sockets Client/Server To Communicate v1.70 [Closed]

> Username: EverydayDiesel  
> Created at: 2019-06-09 14:22:08 UTC  
> Updated at: 2019-06-09 14:47:16 UTC  
> Closed at: 2019-06-09 14:43:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1632  

Hello,  
I am trying to get the boost beast web sockets example client/server to communicate with each other.    
  
server: https://www.boost.org/doc/libs/1_70_0/libs/beast/example/websocket/server/sync/websocket_server_sync.cpp  
  
client: https://www.boost.org/doc/libs/1_70_0/libs/beast/example/websocket/client/sync/websocket_client_sync.cpp  
  
// this error happens on the server (when i start the client)  
![boost error](https://user-images.githubusercontent.com/12397411/59160004-d97baf80-8a96-11e9-89f2-805a663cf9ea.png)  
  
error is thrown on line 70 in 'throw_exception.hpp  
```  
#ifndef BOOST_EXCEPTION_DISABLE  
    throw exception_detail::enable_both( e );  
```  
  
  
  
  
Complete Server Code  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <string>  
#include <thread>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
using namespace std;  
  
// Echoes back all received WebSocket messages  
void  
do_session(tcp::socket& socket)  
{  
    try  
    {  
        // Construct the stream by moving in the socket  
        websocket::stream<tcp::socket> ws{std::move(socket)};  
  
        // Set a decorator to change the Server of the handshake  
        ws.set_option(websocket::stream_base::decorator(  
            [](websocket::response_type& res)  
            {  
                res.set(http::field::server,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                        " websocket-server-sync");  
            }));  
  
        // Accept the websocket handshake  
        ws.accept();  
  
        for(;;)  
        {  
            // This buffer will hold the incoming message  
            beast::flat_buffer buffer;  
  
            // Read a message  
            ws.read(buffer);  
  
            // Echo the message back  
            ws.text(ws.got_text());  
            ws.write(buffer.data());  
        }  
    }  
    catch(beast::system_error const& se)  
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
        auto const address = net::ip::make_address("127.0.0.1");  
        auto const port = static_cast<unsigned short>(std::atoi("3000"));  
  
        // The io_context is required for all I/O  
        net::io_context ioc{1};  
  
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
}  
```  
  
  
Complete client code  
```  
#include <iostream>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <string>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
using namespace std;  
using namespace beast::websocket;  
  
int main(int argc, char** argv)  
{  
    try  
    {  
        auto const host = "127.0.0.1"; // "echo.websocket.org";  
        auto const port = "3000";  
        auto const text = "this is a test";  
  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver{ioc};  
        websocket::stream<tcp::socket> ws{ioc};  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        net::connect(ws.next_layer(), results.begin(), results.end());  
  
        ws.set_option(websocket::stream_base::decorator([](websocket::response_type& req)  
                                                        {  
                                                            req.set(http::field::user_agent,  
                                                                std::string(BOOST_BEAST_VERSION_STRING) +  
                                                                " websocket-client-coro");  
                                                        }));  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/");  
  
        // Send the message  
        ws.write(net::buffer(std::string(text)));  
  
        // This buffer will hold the incoming message  
        beast::flat_buffer buffer;  
  
        // Read a message into our buffer  
        ws.read(buffer);  
  
        // Close the WebSocket connection  
        ws.close(websocket::close_code::normal);  
  
        // If we get here then the connection is closed gracefully  
  
        // The make_printable() function helps print a ConstBufferSequence  
        std::cout << beast::make_printable(buffer.data()) << std::endl;  
  
        cin.get();  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-09 14:32:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1632#issuecomment-500216495  

Yes that exception is normal, it is `websocket::error::closed` indicating that the client has closed the connection. As you can see it is checked in the server (`if(se.code() != websocket::error::closed)`).

---

## Comment 2

> Username: EverydayDiesel  
> Created at: 2019-06-09 14:43:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1632#issuecomment-500217339  

thank you for responding!  
  
one last question.  How can I append text to a beast::flat_buffer?  
  
```  
            beast::flat_buffer buffer;  
  
            // Read a message  
            ws.read(buffer);  
  
            buffer << " text i want to append";  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-06-09 14:44:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1632#issuecomment-500217409  

```  
beast::ostream(buffer) << "text";  
```

---

## Comment 4

> Username: EverydayDiesel  
> Created at: 2019-06-09 14:47:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1632#issuecomment-500217621  

Thank you!!!
