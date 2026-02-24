# #3001 - SSL issues using C++20 Coroutines for websocket client [Closed]

> Username: jayakasadev  
> Created at: 2025-04-15 19:16:22 UTC  
> Updated at: 2025-04-17 16:45:36 UTC  
> Closed at: 2025-04-16 20:23:05 UTC  
> Url: https://github.com/boostorg/beast/issues/3001  

I keep getting the following error when I try to extend the `awaitable` example to use ssl:  
```  
Error: UNINITIALIZED (SSL routines, OPENSSL_internal) [asio.ssl:268435682]  
```  
  
I do not have this issue when I try the `coro-ssl` websocket client example.  
  
Sample code:  
```c++  
#include "boost/beast/src.hpp" // required for boost.beast linking to work properly  
  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/spawn.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
  
#include <cstdlib>  
#include <iostream>  
#include <string>\  
  
namespace beast     = boost::beast;  
namespace http      = beast::http;  
namespace websocket = beast::websocket;  
namespace net       = boost::asio;  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
  
  
  
// The SSL context is required, and holds certificates  
std::shared_ptr<ssl::context> ctx = std::make_shared<ssl::context>(ssl::context::tlsv12);  
  
  
// Sends a WebSocket message and prints the response  
boost::asio::awaitable<void>  
do_session(std::string host, std::string port, std::string text)  
{  
    auto executor = co_await net::this_coro::executor;  
    auto resolver = net::ip::tcp::resolver{ executor };  
    websocket::stream<ssl::stream<beast::tcp_stream>> stream{ executor, *ctx };  
  
    // Look up the domain name  
    auto const results = resolver.resolve(host, port);  
  
    // Set a timeout on the operation  
    beast::get_lowest_layer(stream).expires_after(std::chrono::seconds(30));  
  
    // Make the connection on the IP address we get from a lookup  
    auto ep = beast::get_lowest_layer(stream).connect(results);  
  
    // Set SNI Hostname (many hosts need this to handshake successfully)  
    if(! SSL_set_tlsext_host_name(stream.next_layer().native_handle(), host.c_str()))  
    {  
        co_return;  
    }  
  
    // Set the expected hostname in the peer certificate for verification  
    stream.next_layer().set_verify_callback(ssl::host_name_verification(host));  
  
    // Update the host_ string. This will provide the value of the  
    // Host HTTP header during the WebSocket handshake.  
    // See https://tools.ietf.org/html/rfc7230#section-5.4  
    host += ':' + std::to_string(ep.port());  
  
    // Turn off the timeout on the tcp_stream, because  
    // the websocket stream has its own timeout system.  
    beast::get_lowest_layer(stream).expires_never();  
  
    // Set suggested timeout settings for the websocket  
    stream.set_option(  
        websocket::stream_base::timeout::suggested(beast::role_type::client));  
  
    // Set a decorator to change the User-Agent of the handshake  
    stream.set_option(websocket::stream_base::decorator(  
        [](websocket::request_type& req)  
        {  
            req.set(  
                http::field::user_agent,  
                std::string(BOOST_BEAST_VERSION_STRING) +  
                    " websocket-client-coro");  
        }));  
  
    // Perform the websocket handshake  
    stream.handshake(host, "/");  
  
    // Send the message  
    co_await stream.async_write(net::buffer(text));  
  
    // This buffer will hold the incoming message  
    beast::flat_buffer buffer;  
  
    // Read a message into our buffer  
    co_await stream.async_read(buffer);  
  
    // Close the WebSocket connection  
    co_await stream.async_close(websocket::close_code::normal);  
  
    // If we get here then the connection is closed gracefully  
  
    // The make_printable() function helps print a ConstBufferSequence  
    std::cout << beast::make_printable(buffer.data()) << std::endl;  
}  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv) {  
    auto const host = "advanced-trade-ws.coinbase.com";  
    auto const port = "443";  
    auto const text = R"({"type":"subscribe","channel":"candles","product_ids":["ETH-EUR"]})";  
  
    try {  
    // The io_context is required for all I/O  
    net::io_context ioc;  
    ctx->set_default_verify_paths();  
    ctx->set_verify_mode(ssl::verify_none);  
  
    // Launch the asynchronous operation  
    net::co_spawn(  
        ioc,  
        do_session(host, port, text),  
        [](std::exception_ptr e)  
        {  
            if(e)  
                std::rethrow_exception(e);  
        });  
  
    // Run the I/O service. The call will return when  
    // the socket is closed.  
    ioc.run();  
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

> Username: sehe  
> Created at: 2025-04-15 21:10:35 UTC  
> Updated at: 2025-04-17 16:45:12 UTC  
> Url: https://github.com/boostorg/beast/issues/3001#issuecomment-2807526409  

You are forgetting to do the SSL handshake. E.g. before `stream.set_option(websocket::stream_base::timeout::`... add:  
  
```c++  
    // DO SSL HANDSHAKE  
    co_await stream.next_layer().async_handshake(ssl::stream_base::client);  
```  
  
Now the output reads  
  
```json  
{  
  "channel": "candles",  
  "client_id": "",  
  "timestamp": "2025-04-15T21:10:02.985190032Z",  
  "sequence_num": 0,  
  "events": [  
    {  
      "type": "snapshot",  
      "candles": [  
        {  
          "start": "1744710300",  
          "high": "1450.78",  
          "low": "1445.11",  
          "open": "1445.11",  
          "close": "1450.5",  
          "volume": "19.10792356",  
          "product_id": "ETH-EUR"  
        },  
// ...  
        {  
          "start": "1744751100",  
          "high": "1417.19",  
          "low": "1412.65",  
          "open": "1417.13",  
          "close": "1413.16",  
          "volume": "29.24094415",  
          "product_id": "ETH-EUR"  
        }  
      ]  
    }  
  ]  
}  
```  
  
Note that you're also doing mixing synchronous and asynchronous operations on the same Beast IO objects, which I think I've seen the Beast devs mention is not supported.  
  
Suggesting more fixes:  
  
```c++  
#include <boost/asio.hpp>  
#include <boost/asio/ssl/host_name_verification.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/ssl.hpp>  
#include <iostream>  
  
namespace beast     = boost::beast;  
namespace http      = beast::http;  
namespace websocket = beast::websocket;  
namespace net       = boost::asio;  
namespace ssl       = net::ssl;  
  
// The SSL context is required, and holds certificates  
// Sends a WebSocket message and prints the response  
net::awaitable<void> do_session(std::string host, std::string port, std::string text, ssl::context& ctx) {  
    auto  executor  = co_await net::this_coro::executor;  
    auto  resolver  = net::ip::tcp::resolver{executor};  
    auto  ws        = websocket::stream<ssl::stream<beast::tcp_stream>>{executor, ctx};  
    auto& sslstream = ws.next_layer();  
    auto& stream = get_lowest_layer(ws);  
  
    // Set a timeout on the operation  
    stream.expires_after(std::chrono::seconds(30));  
  
    // Make the connection on the IP address we get from a lookup  
    auto ep = co_await stream.async_connect(co_await resolver.async_resolve(host, port));  
  
    // Set SNI Hostname (many hosts need this to handshake successfully)  
    if (!SSL_set_tlsext_host_name(sslstream.native_handle(), host.c_str()))  
        co_return;  
  
    // Set the expected hostname in the peer certificate for verification  
    sslstream.set_verify_callback(ssl::host_name_verification(host));  
  
    // Update the host_ string. This will provide the value of the  
    // Host HTTP header during the WebSocket handshake.  
    // See https://tools.ietf.org/html/rfc7230#section-5.4  
    host += ':' + std::to_string(ep.port());  
  
    // Turn off the timeout on the tcp_stream, because  
    // the websocket stream has its own timeout system.  
    stream.expires_never();  
  
    // DO SSL HANDSHAKE  
    co_await sslstream.async_handshake(ssl::stream_base::client);  
  
    // Set suggested timeout settings for the websocket  
    ws.set_option(websocket::stream_base::timeout::suggested(beast::role_type::client));  
  
    // Set a decorator to change the User-Agent of the handshake  
    ws.set_option(websocket::stream_base::decorator([](websocket::request_type& req) {  
        req.set(http::field::user_agent, std::string(BOOST_BEAST_VERSION_STRING) + " websocket-client-coro");  
    }));  
  
    // Perform the websocket handshake  
    co_await ws.async_handshake(host, "/");  
  
    // Send the message  
    co_await ws.async_write(net::buffer(text));  
  
    // This buffer will hold the incoming message  
    beast::flat_buffer buffer;  
  
    // Read a message into our buffer  
    co_await ws.async_read(buffer);  
  
    // Close the WebSocket connection  
    co_await ws.async_close(websocket::close_code::normal);  
  
    // If we get here then the connection is closed gracefully  
  
    // The make_printable() function helps print a ConstBufferSequence  
    std::cout << beast::make_printable(buffer.data()) << std::endl;  
}  
  
int main() try {  
    auto const host = "advanced-trade-ws.coinbase.com";  
    auto const port = "443";  
    auto const text = R"({"type":"subscribe","channel":"candles","product_ids":["ETH-EUR"]})";  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
    ssl::context ctx(ssl::context::tlsv12);  
    ctx.set_default_verify_paths();  
    ctx.set_verify_mode(ssl::verify_none);  
  
    // Launch the asynchronous operation  
    net::co_spawn(ioc, do_session(host, port, text, ctx), [](std::exception_ptr e) {  
        if (e)  
            std::rethrow_exception(e);  
    });  
  
    // Run the I/O service. The call will return when  
    // the socket is closed.  
    ioc.run();  
} catch (std::exception const& e) {  
    std::cerr << "Error: " << e.what() << std::endl;  
}  
```  
```

---

## Comment 2

> Username: jayakasadev  
> Created at: 2025-04-16 20:23:05 UTC  
> Url: https://github.com/boostorg/beast/issues/3001#issuecomment-2810681556  

That works.  
Thanks!

---

## Comment 3

> Username: sehe  
> Created at: 2025-04-17 16:45:35 UTC  
> Url: https://github.com/boostorg/beast/issues/3001#issuecomment-2813527340  

@jayakasadev  I missed a pot earlier (async_connect) - updated my sample
