# #3023 - Eventsource support [Closed]

> Username: eaziz4  
> Created at: 2025-07-28 21:00:02 UTC  
> Updated at: 2026-02-09 10:24:18 UTC  
> Closed at: 2026-02-09 10:24:18 UTC  
> Url: https://github.com/boostorg/beast/issues/3023  

I need to open a persistent connection with a server that will constantly feed me data using eventsource.  Is this possible/documented anywhere with beast?

---

## Comment 1

> Username: ashtum  
> Created at: 2025-07-29 10:31:55 UTC  
> Updated at: 2025-07-29 10:32:18 UTC  
> Url: https://github.com/boostorg/beast/issues/3023#issuecomment-3131812329  

Yes, it’s possible. You can use [`http::async_read_header`](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html) and [`http::async_read_some`](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html) to take control of the read loop.  
However, Beast doesn't provide built-in support for EventSource. it's up to you to parse the body and extract the events.  
```C++  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/beast.hpp>  
  
#include <iostream>  
  
using namespace boost;  
namespace http = beast::http;  
namespace ssl = asio::ssl;  
using tcp = asio::ip::tcp;  
  
asio::awaitable<void>  
co_main()  
{  
    auto exec = co_await asio::this_coro::executor;  
    ssl::context ssl_ctx(ssl::context::tlsv12);  
    ssl::stream<tcp::socket> stream(exec, ssl_ctx);  
  
    // connect to server  
    {  
        tcp::resolver resolver(exec);  
        auto endpoints = co_await resolver.async_resolve("stream.wikimedia.org", "https");  
        co_await asio::async_connect(stream.lowest_layer(), endpoints);  
        co_await stream.async_handshake(ssl::stream_base::client);  
    }  
  
    // send request  
    {  
        http::request<http::empty_body> request(http::verb::get, "/v2/stream/recentchange", 11);  
        request.set(http::field::host, "stream.wikimedia.org");  
        request.set(http::field::accept, "text/event-stream");  
        request.set(http::field::cache_control, "no-cache");  
        co_await http::async_write(stream, request);  
    }  
  
    // read header  
    http::response_parser<http::string_body> parser;  
    beast::flat_buffer buffer;  
    co_await http::async_read_header(stream, buffer, parser);  
  
    // stream event  
    for(;;)  
    {  
        co_await http::async_read_some(stream, buffer, parser);  
        std::string& body = parser.get().body();  
        std::cout << body;  
        body.clear();  
    }  
}  
  
int  
main()  
{  
    asio::io_context ioc;  
    asio::co_spawn(  
        ioc,  
        co_main(),  
        [](auto eptr)  
        {  
            if(eptr)  
                std::rethrow_exception(eptr);  
        });  
    ioc.run();  
}  
  
```
