# #1470 - Faild to compile: ‘decorate’ is not a member of ‘websocket’ [Closed]

> Username: UedaTakeyuki  
> Created at: 2019-02-22 01:06:55 UTC  
> Updated at: 2019-06-09 03:23:32 UTC  
> Closed at: 2019-02-22 14:52:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1470  

The sample code in #70 can't be compiled, gcc 8.1.0 report as:  
  
```  
sample.cpp: In function ‘int main(int, char**)’:  
sample.cpp:55:34: error: ‘decorate’ is not a member of ‘websocket’  
         ws.set_option(websocket::decorate(set_subprotocols{"1234"}));  
                                  ^~~~~~~~  
```  
  
Please help! Thanks :)  
  
Sample.cpp is as follows:  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/ssl.hpp>  
  
#include <boost/beast/websocket/stream.hpp>  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
// https://github.com/boostorg/beast/issues/70  
class set_subprotocols  
{  
    std::string s_;  
  
public:  
    explicit  
    set_subprotocols(std::string s)  
        : s_(s) {}  
  
    template<bool isRequest, class Body, class Headers>  
    void  
    operator()(boost::beast::http::message<isRequest, Body, Headers>& m) const  
    {  
        m.fields.replace("X-Custome-Id", s_);  
    }  
};  
  
// Sends a WebSocket message and prints the response  
int main(int argc, char** argv)  
{  
    try  
    {  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver{ioc};  
//        websocket::stream<tcp::socket> ws{ioc};  
        boost::asio::ssl::context ctx{boost::asio::ssl::context::sslv23};  
        ctx.set_verify_mode(boost::asio::ssl::verify_none);  
        websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> ws{ioc, ctx};  
  
  
        boost::asio::ip::tcp::endpoint ep{boost::asio::ip::address::from_string("127.0.0.1"), 8888};  
  
        // add custome header X-Custome-Id  
        ws.set_option(websocket::decorate(set_subprotocols{"1234"}));  
  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```  
  
I've compiled as follows:  
  
```  
g++-8.1.0 -std=c++17 sample.cpp -o wc -I ../boost_1_69_0 -lstdc++ -lpthread -latomic -lcrypto -lssl  
sample.cpp: In function ‘int main(int, char**)’:  
sample.cpp:55:34: error: ‘decorate’ is not a member of ‘websocket’  
         ws.set_option(websocket::decorate(set_subprotocols{"1234"}));  
                                  ^~~~~~~~  
```  
  
Version of Beast is as follows:  
  
```  
cat ../boost_1_69_0/boost/beast/version.hpp   
//  
// Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
#ifndef BOOST_BEAST_VERSION_HPP  
#define BOOST_BEAST_VERSION_HPP  
  
#include <boost/beast/core/detail/config.hpp>  
#include <boost/config.hpp>  
  
/** @def BOOST_BEAST_API_VERSION   
  
    Identifies the API version of Beast.  
  
    This is a simple integer that is incremented by one every  
    time a set of code changes is merged to the develop branch.  
*/  
#define BOOST_BEAST_VERSION 189  
  
#define BOOST_BEAST_VERSION_STRING "Boost.Beast/" BOOST_STRINGIZE(BOOST_BEAST_VERSION)  
  
#endif  
```  
  
Also, I don't forget to star the repository :)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-22 01:13:12 UTC  
> Updated at: 2019-02-22 01:13:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1470#issuecomment-466233651  

The interface for `decorate` changed. In Boost 1.69 it is a function object which you pass into the call to handshake_ex:  
```  
ws.handshake_ex(  
    "localhost",  
    "/",  
    set_subprotocols{"1234"});  
```  
  
However, note that this is once again changing. In Boost 1.70 it will go back to `set_option`:  
  
```  
ws.set_option(websocket::stream_base::decorator(set_subprotocols{"1234"}));  
ws.handshake("localhost", "/");  
```  
  
Sorry about that, growing pains!!!

---

## Comment 2

> Username: UedaTakeyuki  
> Created at: 2019-02-22 07:18:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1470#issuecomment-466299024  

Thank you for your so quick feedback, and sorry for my late feedback.  
I've tried a fix with your pointing about Boost 1.69, but following new error is appeared.  
  
```  
$ g++-8.1.0 -std=c++17 sample.cpp -o wc -I ../boost_1_69_0 -lstdc++ -lpthread -latomic -lcrypto -lssl  
sample.cpp: In instantiation of ‘void set_subprotocols::operator()(boost::beast::http::message<isRequest, Body, Fields>&) const [with bool isRequest = true; Body = boost::beast::http::empty_body; Headers = boost::beast::http::basic_fields<std::allocator<char> >]’:  
../boost_1_69_0/boost/beast/websocket/impl/stream.ipp:648:14:   required from ‘boost::beast::websocket::request_type boost::beast::websocket::stream<NextLayer, deflateSupported>::build_request(boost::beast::websocket::detail::sec_ws_key_type&, boost::beast::string_view, boost::beast::string_view, const Decorator&) [with Decorator = set_subprotocols; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; bool deflateSupported = true; boost::beast::websocket::request_type = boost::beast::http::message<true, boost::beast::http::empty_body, boost::beast::http::basic_fields<std::allocator<char> > >; boost::beast::websocket::detail::sec_ws_key_type = boost::beast::static_string<24>; boost::beast::string_view = boost::basic_string_view<char, std::char_traits<char> >]’  
../boost_1_69_0/boost/beast/websocket/impl/handshake.ipp:408:20:   required from ‘void boost::beast::websocket::stream<NextLayer, deflateSupported>::do_handshake(boost::beast::websocket::response_type*, boost::beast::string_view, boost::beast::string_view, const RequestDecorator&, boost::beast::error_code&) [with RequestDecorator = set_subprotocols; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; bool deflateSupported = true; boost::beast::websocket::response_type = boost::beast::http::message<false, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >; boost::beast::string_view = boost::basic_string_view<char, std::char_traits<char> >; boost::beast::error_code = boost::system::error_code]’  
../boost_1_69_0/boost/beast/websocket/impl/handshake.ipp:368:5:   required from ‘void boost::beast::websocket::stream<NextLayer, deflateSupported>::handshake_ex(boost::beast::string_view, boost::beast::string_view, const RequestDecorator&, boost::beast::error_code&) [with RequestDecorator = set_subprotocols; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; bool deflateSupported = true; boost::beast::string_view = boost::basic_string_view<char, std::char_traits<char> >; boost::beast::error_code = boost::system::error_code]’  
../boost_1_69_0/boost/beast/websocket/impl/handshake.ipp:303:5:   required from ‘void boost::beast::websocket::stream<NextLayer, deflateSupported>::handshake_ex(boost::beast::string_view, boost::beast::string_view, const RequestDecorator&) [with RequestDecorator = set_subprotocols; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; bool deflateSupported = true; boost::beast::string_view = boost::basic_string_view<char, std::char_traits<char> >]’  
sample.cpp:59:37:   required from here  
sample.cpp:32:11: error: ‘struct boost::beast::http::message<true, boost::beast::http::empty_body, boost::beast::http::basic_fields<std::allocator<char> > >’ has no member named ‘fields’; did you mean ‘find’?  
         m.fields.replace("X-Custome-Id", s_);  
         ~~^~~~~~  
         find  
```  
  
Are there any other point which I shoud take care? Thank you for your help.  
  
Fixed **sample.cpp** is as follows:  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/ssl.hpp>  
  
#include <boost/beast/websocket/stream.hpp>  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
// https://github.com/boostorg/beast/issues/70  
class set_subprotocols  
{  
    std::string s_;  
  
public:  
    explicit  
    set_subprotocols(std::string s)  
        : s_(s) {}  
  
    template<bool isRequest, class Body, class Headers>  
    void  
    operator()(boost::beast::http::message<isRequest, Body, Headers>& m) const  
    {  
        m.fields.replace("X-Custome-Id", s_);  
    }  
};  
  
// Sends a WebSocket message and prints the response  
int main(int argc, char** argv)  
{  
    try  
    {  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver{ioc};  
//        websocket::stream<tcp::socket> ws{ioc};  
        boost::asio::ssl::context ctx{boost::asio::ssl::context::sslv23};  
        ctx.set_verify_mode(boost::asio::ssl::verify_none);  
        websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> ws{ioc, ctx};  
  
  
        boost::asio::ip::tcp::endpoint ep{boost::asio::ip::address::from_string("127.0.0.1"), 8888};  
  
        // add custome header X-Custome-Id  
//        ws.set_option(websocket::decorate(set_subprotocols{"1234"}));  
  
        // connect the underlying TCP/IP socket  
        ws.next_layer().next_layer().connect(ep);  
  
        // perform SSL handshake  
        ws.next_layer().handshake(boost::asio::ssl::stream_base::client);  
  
        ws.handshake_ex(  
            "localhost",  
            "/",  
            set_subprotocols{"1234"});  
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
> Created at: 2019-02-22 12:40:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1470#issuecomment-466383430  

There's no more `fields` data member, the message is derived from the `Fields` class now so you can just write  
```  
m.set("X-Custome-Id", s_);  
```

---

## Comment 4

> Username: UedaTakeyuki  
> Created at: 2019-02-22 14:44:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1470#issuecomment-466420293  

It's working completely! Thank you for your kindly help!  
Please close this issue if that's all right with you.

---

## Comment 5

> Username: EverydayDiesel  
> Created at: 2019-06-09 01:54:13 UTC  
> Updated at: 2019-06-09 02:04:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1470#issuecomment-500178408  

> The interface for `decorate` changed. In Boost 1.69 it is a function object which you pass into the call to handshake_ex:  
>   
> ```  
> ws.handshake_ex(  
>     "localhost",  
>     "/",  
>     set_subprotocols{"1234"});  
> ```  
>   
> However, note that this is once again changing. In Boost 1.70 it will go back to `set_option`:  
>   
> ```  
> ws.set_option(websocket::stream_base::decorator(set_subprotocols{"1234"}));  
> ws.handshake("localhost", "/");  
> ```  
>   
> Sorry about that, growing pains!!!  
  
  
Hello,  
  
I tried to use the ws.set_option but it still tells me that websocket::stream_base has not been declared in version 1.70.  
  
  
  
  
```  
  
-------------- Build: Debug in WebSocketsClientTest (compiler: GNU GCC Compiler)---------------  
  
g++ -Wall -fexceptions -g  -c /home/WebSocketsClientTest/main.cpp -o obj/Debug/main.o  
/home/WebSocketsClientTest/main.cpp: In function ‘int main(int, char**)’:  
/home/WebSocketsClientTest/main.cpp:76:23: error: ‘decorate’ was not declared in this scope  
         ws.set_option(decorate(set_subprotocols{"mqtt"}));  
                       ^~~~~~~~  
/home/WebSocketsClientTest/main.cpp:76:23: note: suggested alternative: ‘setstate’  
         ws.set_option(decorate(set_subprotocols{"mqtt"}));  
                       ^~~~~~~~  
                       setstate  
/home/WebSocketsClientTest/main.cpp:97:29: error: ‘make_printable’ is not a member of ‘beast’  
         std::cout << beast::make_printable(buffer.data()) << std::endl;  
                             ^~~~~~~~~~~~~~  
Process terminated with status 1 (0 minute(s), 2 second(s))  
2 error(s), 0 warning(s) (0 minute(s), 2 second(s))  
   
  
```  
  
   
  
  
  
  
  
  
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
  
class set_subprotocols  
{  
    std::string s_;  
  
public:  
    explicit  
    set_subprotocols(std::string s)  
        : s_(s) {}  
  
    template<bool isRequest, class Body, class Headers>  
    void  
    operator()(boost::beast::http::message<isRequest, Body, Headers>& m) const  
    {  
        m.fields.replace("X-Custome-Id", s_);  
    }  
};  
  
int main(int argc, char** argv)  
{  
    try  
    {  
        // Check command line arguments.  
        if(argc != 4)  
        {  
            std::cerr <<  
                "Usage: websocket-client-sync <host> <port> <text>\n" <<  
                "Example:\n" <<  
                "    websocket-client-sync echo.websocket.org 80 \"Hello, world!\"\n";  
            return EXIT_FAILURE;  
        }  
        auto const host = argv[1];  
        auto const port = argv[2];  
        auto const text = argv[3];  
  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver{ioc};  
        websocket::stream<tcp::socket> ws{ioc};  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        net::connect(ws.next_layer(), results.begin(), results.end());  
  
        // Set a decorator to change the User-Agent of the handshake  
        //ws.set_option(websocket::stream_base::decorator(  
        //    [](websocket::request_type& req)  
        //    {  
        //        req.set(http::field::user_agent,  
        //            std::string(BOOST_BEAST_VERSION_STRING) +  
        //                " websocket-client-coro");  
        //    }));  
  
        ws.set_option(decorate(set_subprotocols{"mqtt"}));  
        //ws.set_option(websocket::stream_base::decorator(set_subprotocols{"1234"}));  
  
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

## Comment 6

> Username: vinniefalco  
> Created at: 2019-06-09 02:41:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1470#issuecomment-500180077  

> it still tells me that websocket::stream_base has not been declared in version 1.70.  
  
It works fine in the examples:  
https://github.com/boostorg/beast/blob/6af4c01e5662926372a7e43f3194fe01846a28dd/example/advanced/server/advanced_server.cpp#L244  
  
Are you sure that the beast header being included is the 1.70 header? Or perhaps on your system, a different directory with an older version of boost is being included?

---

## Comment 7

> Username: EverydayDiesel  
> Created at: 2019-06-09 02:48:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1470#issuecomment-500180367  

You are right, I need to figure out how to remove all the versions of boost and reinstall  
  
cat /usr/local/include/boost/version.hpp | grep "BOOST_LIB_VERSION"//  BOOST_LIB_VERSION must be defined to be the same as BOOST_VERSION  
#define BOOST_LIB_VERSION "1_69"

---

## Comment 8

> Username: EverydayDiesel  
> Created at: 2019-06-09 03:23:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1470#issuecomment-500181796  

For someone else looking to do this I first deleted  
/usr/local/lib/libboost*  
/usr/local/include/boost  
  
reinstalled  
./bootstrap.sh --prefix=/usr/local  
./b2  
sudo ./b2 install  
  
 cat /usr/local/include/boost/version.hpp | grep "BOOST_LIB_VERSION"  
//  BOOST_LIB_VERSION must be defined to be the same as BOOST_VERSION  
#define BOOST_LIB_VERSION "1_70"  
  
  
  
I also had to change  
decorate to decorator   
ws.set_option(websocket::stream_base::decorator(set_subprotocols{"mqtt"}));
