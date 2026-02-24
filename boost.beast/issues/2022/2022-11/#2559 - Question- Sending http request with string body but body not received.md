# #2559 - Question: Sending http request with string body but body not received [Closed]

> Username: felixjulianheitmann  
> Created at: 2022-11-06 17:14:35 UTC  
> Updated at: 2022-11-06 17:26:09 UTC  
> Closed at: 2022-11-06 17:24:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2559  

EDIT: Solution: Call `req.prepare_payload()` on a request before sending it.  
  
### Version of Beast  
330  
  
### Steps necessary to reproduce the problem  
I have a MWE client like this:  
```c++  
#include <iostream>  
  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/url.hpp>  
  
auto main() -> int  
{  
    namespace http  = boost::beast::http;  
    namespace beast = boost::beast;  
    namespace asio  = boost::asio;  
    namespace ssl   = boost::asio::ssl;  
  
    http::request< http::string_body > req;  
    req.method( http::verb::post );  
    req.set( http::field::content_type, "text/html" );  
    req.target( "/some_endpoint" );  
    req.body() = "Hello world!";  
    std::cout << req << std::endl;  
  
    boost::asio::io_context io;  
  
    asio::co_spawn(  
        io,  
        [&]() -> asio::awaitable< void > {  
            ssl::context                           ctx_( ssl::context::tlsv12 );  
            beast::ssl_stream< beast::tcp_stream > stream( io, ctx_ );  
            ctx_.set_verify_mode( asio::ssl::verify_peer );  
  
            auto results =  
                co_await asio::ip::tcp::resolver( io ).async_resolve( "localhost", "8080", asio::use_awaitable );  
            co_await beast::get_lowest_layer( stream ).async_connect( results, asio::use_awaitable );  
            co_await stream.async_handshake( ssl::stream_base::client, asio::use_awaitable );  
  
            auto bytes_sent = co_await http::async_write( stream, req, asio::use_awaitable );  
  
            std::cout << "Send bytes: " << bytes_sent << std::endl;  
            boost::system::error_code ec;  
            co_await stream.async_shutdown( asio::redirect_error( asio::use_awaitable, ec ) );  
        },  
        [&]( std::exception_ptr e ) {  
            io.stop();  
            if ( e )  
                std::rethrow_exception( e );  
        } );  
  
  
    io.run();  
}  
```  
  
It connects to the server and prints the following:  
```  
POST /some_endpoint HTTP/1.1  
Content-Type: text/html  
  
Hello world!  
Send bytes: 69  
```  
  
The corresponding server code is slightly larger but accepts the connection, does the handshake and an `http::async_read` which reads the request into a sufficiently large buffer (1MB).  
Printing the request on server side gives:  
```  
POST /some_endpoint HTTP/1.1  
Content-Type: text/html  
  
Received bytes: 57  
```  
  
It does not read the body (12 bytes - without terminating 0 character) and I have no idea why. Likely, I am missing something obvious. If anyone can point me into the right direction, I'd be glad.  
  
If you need the server side code as well, let me know.  
  
Thanks in advance!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-11-06 17:17:40 UTC  
> Updated at: 2022-11-06 17:18:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2559#issuecomment-1304849677  

Did you call `req.prepare_payload()` to set Content-Length?  
  
Also that's technically not a valid request. It requires the "Host" field. But your server might be accepting of it anyway.

---

## Comment 2

> Username: felixjulianheitmann  
> Created at: 2022-11-06 17:24:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2559#issuecomment-1304851083  

That did the trick! Thanks, I knew it had to be something trivial!
