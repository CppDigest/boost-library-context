# #3045 - Warning: memcpy forming offset [28, 128] is out of the bounds [0, 28] of object 'endpoint' [Open]

> Username: pps83  
> Created at: 2025-10-13 18:24:10 UTC  
> Updated at: 2025-10-14 16:59:51 UTC  
> Url: https://github.com/boostorg/beast/issues/3045  

I have a warning that I get when compiling code that uses boost::beast. This happens only on msys2 when compiling with g++  (my version is 15.1.0). This warning doesn't really make much sense as it doesn't even show what code specifically leads to it:  
  
```  
mtlro@MAIN-NOTE MINGW64 /d/work-pps/backtest-engine/vs  
$ nice -20 cmake --build $BUILD  
[3/4] Building CXX object CMakeFiles/test.dir/src/test/WebSocketTest.cpp.obj  
In file included from D:/work-pps/boost_1_89_0/boost/asio/ip/basic_resolver.hpp:32,  
                 from D:/work-pps/boost_1_89_0/boost/asio/ip/tcp.hpp:25,  
                 from D:/work-pps/boost_1_89_0/boost/beast/core/tcp_stream.hpp:17,  
                 from D:/work-pps/boost_1_89_0/boost/beast/core.hpp:49,  
                 from D:/work-pps/boost_1_89_0/boost/beast.hpp:15,  
                 from D:/work-pps/backtest-engine/src/test/WebSocketTest.cpp:1:  
D:/work-pps/boost_1_89_0/boost/asio/ip/basic_resolver_results.hpp: In static member function 'static boost::asio::ip::basic_resolver_results<InternetProtocol> boost::asio::ip::basic_resolver_results<InternetProtocol>::create(boost::asio::detail::addrinfo_type*, const std::string&, const std::string&) [with InternetProtocol = boost::asio::ip::tcp]':  
D:/work-pps/boost_1_89_0/boost/asio/ip/basic_resolver_results.hpp:139:15: warning: 'void* memcpy(void*, const void*, size_t)' forming offset [28, 128] is out of the bounds [0, 28] of object 'endpoint' with type 'boost::asio::ip::tcp::endpoint' {aka 'boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>'} [-Warray-bounds=]  
  139 |         memcpy(endpoint.data(), address_info->ai_addr,  
      |         ~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  140 |             address_info->ai_addrlen);  
      |             ~~~~~~~~~~~~~~~~~~~~~~~~~  
D:/work-pps/boost_1_89_0/boost/asio/ip/basic_resolver_results.hpp:137:45: note: 'endpoint' declared here  
  137 |         typename InternetProtocol::endpoint endpoint;  
      |                                             ^~~~~~~~  
```  
  
Here's the trimmed code that produces this warning:  
```  
#include <boost/beast.hpp>  
  
namespace net = boost::asio;  
namespace ssl = net::ssl;  
namespace beast = boost::beast;  
namespace http = beast::http;  
namespace websocket = beast::websocket;  
  
void test()  
{  
    using tcp = net::ip::tcp;  
    net::io_context ioc_ws;  
    tcp::resolver resolver_ws{ioc_ws};  
    std::unique_ptr<websocket::stream<tcp::socket>> ws;  
  
    ws = std::make_unique<websocket::stream<tcp::socket>>(ioc_ws);  
    auto results = resolver_ws.resolve("127.0.0.1", "80");  
    net::connect(ws->next_layer(), results.begin(), results.end());  
    ws->handshake("127.0.0.1", "/ws");  
}  
```  
  
I use boost 1.89. I had the same warning with older versions as well.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-10-13 20:13:57 UTC  
> Url: https://github.com/boostorg/beast/issues/3045#issuecomment-3398925474  

What happens when you remove beast entirely and instead of using a websocket::stream use an `asio::ip::tcp::socket` ?

---

## Comment 2

> Username: pps83  
> Created at: 2025-10-14 01:03:49 UTC  
> Updated at: 2025-10-14 15:23:47 UTC  
> Url: https://github.com/boostorg/beast/issues/3045#issuecomment-3399582344  

I tried to change to this:  
```  
#include <boost/asio.hpp>  
  
namespace net = boost::asio;  
  
void test()  
{  
    using tcp = net::ip::tcp;  
    net::io_context ioc_ws;  
    tcp::resolver resolver_ws{ioc_ws};  
    std::unique_ptr<boost::asio::ip::tcp::socket> ws;  
  
    ws = std::make_unique<boost::asio::ip::tcp::socket>(ioc_ws);  
    auto results = resolver_ws.resolve("127.0.0.1", "80");  
    net::connect(ws->lowest_layer(), results.begin(), results.end());  
}  
```  
  
got the same error

---

## Comment 3

> Username: ashtum  
> Created at: 2025-10-14 05:54:28 UTC  
> Url: https://github.com/boostorg/beast/issues/3045#issuecomment-3400225136  

This looks like a Asio issue. Could you please open an issue here: https://github.com/chriskohlhoff/asio/issues?  
What compiler flags are you using? It would help if you could come up with a reproducible example on https://godbolt.org/.

---

## Comment 4

> Username: sehe  
> Created at: 2025-10-14 10:22:12 UTC  
> Url: https://github.com/boostorg/beast/issues/3045#issuecomment-3401121984  

To me it looks like a MINGW issue (false positive diagnostic) - but compiler explorer doesn't allow us to repro https://godbolt.org/z/8xbKdshYW

---

## Comment 5

> Username: pps83  
> Created at: 2025-10-14 15:16:21 UTC  
> Updated at: 2025-10-14 16:59:51 UTC  
> Url: https://github.com/boostorg/beast/issues/3045#issuecomment-3402378991  

> This looks like a Asio issue. Could you please open an issue here: https://github.com/chriskohlhoff/asio/issues? What compiler flags are you using? It would help if you could come up with a reproducible example on https://godbolt.org/.  
  
Not sure if it's false diag, but it's too specific with these offsets. Looks very strange imo.  
  
> What compiler flags are you using? It would help if you could come up with a reproducible example on https://godbolt.org/.  
  
Godbolt has issues using libs with non-linux targets afaik (cannot use boost basically with g++ on windows). I use regular cmake build, here is trimmed cmd that results in the warning:  
  
```  
D:\msys64\mingw64\bin\c++.exe -ID:/work-pps/boost_1_89_0 -O3 -DNDEBUG -std=c++23 -march=skylake -Wformat -Wall -Werror=format -x c++ -o CMakeFiles/test.dir/src/test/WebSocketTest.cpp.obj -c D:/work-pps/backtest-engine/src/test/WebSocketTest.cpp  
```
