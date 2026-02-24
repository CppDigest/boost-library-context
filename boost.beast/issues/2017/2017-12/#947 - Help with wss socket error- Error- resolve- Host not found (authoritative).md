# #947 - Help with wss socket error: Error: resolve: Host not found (authoritative) [Closed]

> Username: ghost  
> Created at: 2017-12-23 04:23:32 UTC  
> Updated at: 2017-12-23 15:37:20 UTC  
> Closed at: 2017-12-23 15:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/947  

Hello,  
  
trying to connect to a websocket...and cant understand why I am getting the following error  
Error: resolve: Host not found (authoritative)  
  
I am using the lastest 1.66 boost version with beast included.   
  
this is what I am trying to connect to.   
https://docs.bitfinex.com/v2/docs/ws-general   
  
any help would be appreciated.   
  
```  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
using tcp = boost::asio::ip::tcp;  
namespace websocket = boost::beast::websocket;  
  
int main(int argc, char** argv)  
{  
    try  
    {  
        auto const host = "wss://api.bitfinex.com/ws/2";  
        auto const port = "443";  
  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver {ioc};  
        websocket::stream<tcp::socket> ws{ioc};  
          
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        boost::asio::connect(ws.next_layer(), results.begin(), results.end());  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/");  
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
> Created at: 2017-12-23 04:36:59 UTC  
> Url: https://github.com/boostorg/beast/issues/947#issuecomment-353706592  

`"wss://api.bitfinex.com/ws/2"` is not a valid host.  
  
Use `"api.bitfinex.com"` for the host, and `"wss"` for the port. Then, use this call to handshake:  
  
```  
    // Perform the websocket handshake  
    ws.handshake(host, "/ws/2");  
```

---

## Comment 2

> Username: ghost  
> Created at: 2017-12-23 04:43:09 UTC  
> Updated at: 2017-12-23 04:46:17 UTC  
> Url: https://github.com/boostorg/beast/issues/947#issuecomment-353706781  

Thank you for the reply. Followed those steps and now getting "Error: resolve: Service not found".  
  
```  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
using tcp = boost::asio::ip::tcp;  
namespace websocket = boost::beast::websocket;  
  
int main(int argc, char** argv)  
{  
    try  
    {  
        auto const host = "api.bitfinex.com";  
        auto const port = "wss";  
  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver {ioc};  
        websocket::stream<tcp::socket> ws{ioc};  
          
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        boost::asio::connect(ws.next_layer(), results.begin(), results.end());  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/ws/2");  
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

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-23 04:45:58 UTC  
> Url: https://github.com/boostorg/beast/issues/947#issuecomment-353706853  

Try `"443"` for the port

---

## Comment 4

> Username: ghost  
> Created at: 2017-12-23 08:53:13 UTC  
> Url: https://github.com/boostorg/beast/issues/947#issuecomment-353714888  

thank you, looks like it is connecting now however failing on the handshake with the following code...  
ws.handshake(host, "/ws/2");  
  
Error: WebSocket upgrade handshake failed

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-12-23 14:36:35 UTC  
> Url: https://github.com/boostorg/beast/issues/947#issuecomment-353729244  

You are connecting to a Secure WebSocket port (443, "wss"). This means you need to use `boost::asio::ssl::stream` instead of a regular socket.  You should be able to adapt this example to make that work:  
  
http://www.boost.org/doc/libs/1_66_0/libs/beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp

---

## Comment 6

> Username: ghost  
> Created at: 2017-12-23 15:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/947#issuecomment-353732499  

brilliant, its working now. thanks very much for the guidance.
