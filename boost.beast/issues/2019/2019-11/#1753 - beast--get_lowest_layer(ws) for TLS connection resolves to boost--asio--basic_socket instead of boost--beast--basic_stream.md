# #1753 - beast::get_lowest_layer(ws) for TLS connection resolves to boost::asio::basic_socket instead of boost::beast::basic_stream [Closed]

> Username: h-bex  
> Created at: 2019-11-01 14:56:02 UTC  
> Updated at: 2019-11-01 15:17:08 UTC  
> Closed at: 2019-11-01 15:17:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1753  

### Version of Beast  
`BOOST_BEAST_VERSION 266` from boost-1_71  
  
### Steps necessary to reproduce the problem  
```  
#define _WIN32_WINNT 0x0601  
#include <boost/asio.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/ssl.hpp>  
  
namespace beast     = boost::beast;         // from <boost/beast.hpp>  
namespace http      = beast::http;          // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket;     // from <boost/beast/websocket.hpp>  
namespace net       = boost::asio;          // from <boost/asio.hpp>  
namespace ssl       = boost::asio::ssl;     // from <boost/asio/ssl.hpp>  
using tcp           = boost::asio::ip::tcp; // from <boost/asio/ip/tcp.hpp>  
  
void  
buggyWebSocketViaSSL(net::io_context &                           ioc,  
                     ssl::context &                              ctx,  
                     const net::ip::basic_resolver_results<tcp> &results)  
{  
    // These objects perform our I/O  
    websocket::stream<beast::ssl_stream<tcp::socket>> webSocketStream{ioc, ctx};  
  
    // Irrelevant code removed ...  
  
    // BUG: The connect below is from boost::asio::basic_socket<...>  
    // Expectation: it is the connect from boost::beast::basic_stream  
    beast::get_lowest_layer(webSocketStream).connect(*results.begin( ));  
  
    // You can evaluate this by calling the following line which does not  
    // compile but should:  
    // beast::get_lowest_layer(webSocketStream).connect(results);**  
  
    // Irrelevant code remove ...  
}  
  
void  
onlyForReferenceSSLConnection(  
  net::io_context &                           ioc,  
  ssl::context &                              ctx,  
  const std::string &                         host,  
  const net::ip::basic_resolver_results<tcp> &results,  
  const std::string &                         target,  
  const int &                                 version)  
{  
    // These objects perform our I/O  
    beast::ssl_stream<beast::tcp_stream> stream{ioc, ctx};  
  
    // This connect is (as expected) from boost::beast::basic_stream  
    beast::get_lowest_layer(stream).connect(results);  
  
    // Irrelevant code remove ...  
}  
  
// Only for compiling the stuff  
int  
main( )  
{  
}  
```  
### All relevant compiler information  
Visual Studio 2019 16.3.7 (Visual C** 2019)  
#### Relevant Command Line:   
/JMC /permissive- /GS /W3 /Zc:wchar_t /I"...\include\boost-1_71\" /ZI /Gm- /Od /sdl /Fd"x64\Debug\vc142.pdb" /Zc:inline /fp:precise /D "_DEBUG" /D "_CONSOLE" /D "BOOST_DYN_LINK" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /WX- /Zc:forScope /RTC1 /Gd /MDd /std:c++17 /FC /Fa"x64\Debug\" /EHsc /nologo /Fo"x64\Debug\" /diagnostics:column  
  
### Expected results  
`beast::get_lowest_layer(...)` shall return `boost::beast::basic_stream` under any circumstances. All other behavior is not expected and as far as I understand also not documented.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-01 15:02:36 UTC  
> Updated at: 2019-11-01 15:04:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1753#issuecomment-548821275  

You wrote  
```  
websocket::stream<beast::ssl_stream<tcp::socket>> webSocketStream{ioc, ctx};      
```  
  
The correct behavior for `get_lowest_layer(webSocketStream)` is to return `tcp::socket&`. If you are expecting to see `beast::tcp_stream&`, your must declare the websocket stream thusly:  
```  
websocket::stream<beast::ssl_stream<beast::tcp_stream>> webSocketStream{ioc, ctx};      
```

---

## Comment 2

> Username: h-bex  
> Created at: 2019-11-01 15:17:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1753#issuecomment-548826934  

Hello Vinnie,  
  
Uups; You are absolutly right. It so easy to get this wrong. Perhaps get_lowest_layer shall be a little bit more restricted...
