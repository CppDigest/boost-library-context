# #1429 - simple http client sync unstable [Closed]

> Username: MaximNM  
> Created at: 2019-02-04 13:12:01 UTC  
> Updated at: 2019-04-18 00:44:32 UTC  
> Closed at: 2019-04-18 00:44:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1429  

Hello,  
  
I took code from example of http_client_sync and split the code to httpconnect, httprequest, httpclose functions in order to reopen connection if http server closed it after it has reached keep_alive_requests limit in other words I'd like to use a connection for maximum amount of requests.  
  
Please see code below:  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
namespace beast = boost::beast;     // from <boost/beast.hpp>  
namespace http = beast::http;       // from <boost/beast/http.hpp>  
namespace net = boost::asio;        // from <boost/asio.hpp>  
using tcp = net::ip::tcp;           // from <boost/asio/ip/tcp.hpp>  
  
tcp::socket httpconnect(const std::string& host, const std::string& port)  
{  
    // The io_context is required for all I/O  
    net::io_context ioc;  
      
    // These objects perform our I/O  
    tcp::resolver resolver{ioc};  
    tcp::socket socket{ioc};  
      
    // Look up the domain name  
    auto const results = resolver.resolve(host, port);  
      
    // Make the connection on the IP address we get from a lookup  
    net::connect(socket, results.begin(), results.end());  
      
    return socket;  
}  
  
auto httprequest(tcp::socket& socket, const std::string& host, const std::string& target)  
{  
    // Set up an HTTP GET request message  
    int version = 11;  
    http::request<http::empty_body> req{http::verb::get, target, version};  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    //req.keep_alive(true);  
      
    // Send the HTTP request to the remote host  
    http::response<http::string_body> res;  
    beast::error_code ec;  
    do {  
        http::write(socket, req);  
      
        // This buffer is used for reading and must be persisted  
        beast::flat_buffer buffer;  
          
        // Receive the HTTP response  
        http::read(socket, buffer, res, ec);  
        if (ec && ec == beast::http::error::end_of_stream)  
            socket = httpconnect(host, "80");  
          
    } while (ec);  
      
    return res;  
}  
  
void httpclose(tcp::socket& socket)  
{  
    // Gracefully close the socket  
    beast::error_code ec;  
    socket.shutdown(tcp::socket::shutdown_both, ec);  
      
    // not_connected happens sometimes  
    // so don't bother reporting it.  
    //  
    if(ec && ec != beast::errc::not_connected)  
        throw beast::system_error{ec};  
      
    // If we get here then the connection is closed gracefully  
}  
  
// Performs an HTTP GET and prints the response  
int main(int argc, char** argv)  
{  
    try  
    {  
        // Check command line arguments.  
        /*  
        if(argc != 4 && argc != 5)  
        {  
            std::cerr <<  
            "Usage: http-client-sync <host> <port> <target> [<HTTP version: 1.0 or 1.1(default)>]\n" <<  
            "Example:\n" <<  
            "    http-client-sync www.example.com 80 /\n" <<  
            "    http-client-sync www.example.com 80 / 1.0\n";  
            return EXIT_FAILURE;  
        }*/  
          
        std::string host = "httpbin.org";  
        std::cout << "Connect to: " << host << " ... ";  
        tcp::socket socket(httpconnect(host, "80"));  
        std::cout << "connected\n";  
          
        std::string target = "/";  
        for (int i = 1; i <= 1000; ++i) {  
            std::cout << "HTTP Request: " << target << ' ' << i << std::endl;  
            if ( !socket.is_open() )  
                throw std::runtime_error("Socket is closed");  
            auto res = httprequest(socket, host, target);  
            std::cout << "HTTP Response Status: " << res.result() << std::endl;  
            //std::cout << "HTTP Body:\n" << res.body() << std::endl;  
        }  
        httpclose(socket);  
        std::cout << "Connection closed\n";  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Exception: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```  
  
boost: stable 1.68.0  
BOOST_BEAST_VERSION 181  
  
Xcode Version 10.1 (10B61)  
clang -v  
Apple LLVM version 10.0.0 (clang-1000.11.45.5)  
Target: x86_64-apple-darwin18.2.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
compiler flags - GNU C++17  
  
I found that same times it works but some times it's terminated at error_code.hpp in line:  
  
      template<typename ErrorCodeEnum>  
        typename boost::enable_if<is_error_code_enum<ErrorCodeEnum>, error_code>::type &  
          operator=( ErrorCodeEnum val ) BOOST_SYSTEM_NOEXCEPT  
      {  
        *this = make_error_code(val); // !!! HERE !!!  
        return *this;  
      }  
It's usually terminated on request step 301 or 401 if keep_alive_requests on server set to 100.  
May be there is some mistake in my code could you please check.  
  
Thanks in advance,  
Maxim

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-04 13:50:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460256054  

Do you understand the comment here?  
```  
        // This buffer is used for reading and must be persisted  
        beast::flat_buffer buffer;  
```  
  
You aren't persisting the buffer...

---

## Comment 2

> Username: MaximNM  
> Created at: 2019-02-04 13:57:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460258459  

Thanks!  
I have just started to learn the Asio and your great Beast libraries.  
Could you please recommend what kind of buffer I have to use here?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-04 14:04:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460260743  

> Could you please recommend what kind of buffer I have to use here?  
  
The problem is not the choice of buffer but the lifetime. You have to use the *same* object to read each message, until the connection is closed (then you need to clear the buffer or destroy it and use a new   
one). This is explained in the documentation:  
  
_"To hold this surplus data, all stream read operations use a passed-in DynamicBuffer which must be persisted between calls until the end of stream is reached or the stream object is destroyed. Each read operation may consume bytes remaining in the buffer, and leave behind new bytes."_  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_http/message_stream_operations.html#beast.using_http.message_stream_operations.reading

---

## Comment 4

> Username: MaximNM  
> Created at: 2019-02-04 14:33:33 UTC  
> Updated at: 2019-02-04 15:14:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460270321  

Thanks Vinnie!  
  
I reuse the flat_buffer only if I need to reconnect in other cases the buffer is created per request.  
  
I clean the buffer but it doesn't help please see below:  
```  
auto httprequest(tcp::socket& socket, const std::string& host, const std::string& target)  
{  
    // Set up an HTTP GET request message  
    int version = 11;  
    http::request<http::empty_body> req{http::verb::get, target, version};  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    //req.keep_alive(true);  
      
    // Send the HTTP request to the remote host  
    http::response<http::string_body> res;  
    beast::error_code ec;  
    do {  
        http::write(socket, req);  
      
        // This buffer is used for reading and must be persisted  
        beast::flat_buffer buffer;  
          
        // Receive the HTTP response  
        http::read(socket, buffer, res, ec);  
        if ( ec )  
            if ( ec == beast::http::error::end_of_stream )  
                socket = httpconnect(host, "80");  
            else  
                throw beast::system_error{ec};  
          
        buffer.consume(buffer.size());  
          
    } while (ec);  
      
    return res;  
}   
```  
  
I got Thread 1: EXC_BAD_ACCESS (code=EXC_I386_GPFLT) error on 201th request.  
  
I also surprise why beast doesn't throw an exception and is terminated by os.

---

## Comment 5

> Username: MaximNM  
> Created at: 2019-02-04 15:04:54 UTC  
> Updated at: 2019-02-04 15:17:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460281588  

I found another issue after I had removed the reconnect I had to get an exception for instance beast::http::error::end_of_stream but my app was terminated  when trying to get respond for 101th request.  
  
One additional remark my app is terminated randomly around 1 time for 8 try.  
  
Code:  
```  
auto httprequest(tcp::socket& socket, const std::string& host, const std::string& target)  
{  
    // Set up an HTTP GET request message  
    int version = 11;  
    http::request<http::empty_body> req{http::verb::get, target, version};  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    //req.keep_alive(true);  
      
    // Send the HTTP request to the remote host  
    http::response<http::string_body> res;  
    http::write(socket, req);  
      
    // This buffer is used for reading and must be persisted  
    beast::flat_buffer buffer;  
          
    // Receive the HTTP response  
    http::read(socket, buffer, res);  
  
    return res;  
}  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-02-04 15:19:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460287148  

You are throwing away the `flat_buffer` after each response, I told you that's wrong.

---

## Comment 7

> Username: MaximNM  
> Created at: 2019-02-04 16:15:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460308775  

Thanks a lot for your support!  
  
I made buffer global variable just for test. Now I can produce 300-500 requests but still got same error.  
I suppose I have to reset the buffer after I have opened a new connection I have tried buffer.consume(buffer.size()) but without success.  
  
Why I really get the error ?  
How can I reuse the buffer correctly for a new connection?  
  
Please see modifyed code below:  
```  
beast::flat_buffer buffer;  
  
auto httprequest(tcp::socket& socket, const std::string& host, const std::string& target)  
{  
    // Set up an HTTP GET request message  
    int version = 11;  
    http::request<http::empty_body> req{http::verb::get, target, version};  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    //req.keep_alive(true);  
      
    // Send the HTTP request to the remote host  
    http::response<http::string_body> res;  
    beast::error_code ec;  
    do {  
        http::write(socket, req);  
      
        // This buffer is used for reading and must be persisted  
        //beast::flat_buffer buffer;  
          
        // Receive the HTTP response  
        http::read(socket, buffer, res, ec);  
        if ( ec )  
            if ( ec == beast::http::error::end_of_stream ) {  
                httpclose(socket);  
                //buffer.commit(buffer.size());  
                //buffer.consume(buffer.size());  
                socket = httpconnect(host, "80");  
            } else  
                throw beast::system_error{ec};  
          
    } while (ec);  
      
    return res;  
}  
```

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-02-04 16:18:50 UTC  
> Updated at: 2019-02-04 16:19:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460310143  

after closing the socket, use `buffer.consume(buffer.size())`  
  
The buffer should probably be a parameter to `httprequest`, not a global variable.

---

## Comment 9

> Username: MaximNM  
> Created at: 2019-02-04 16:42:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460319533  

Thanks  
  
I did as you recommended but I'm still getting same error on 101th, 201th or 501th request.  
What did I miss now?  
  
modified code:  
```  
auto httprequest(tcp::socket& socket, beast::flat_buffer& buffer, const std::string& host, const std::string& target)  
{  
    // Set up an HTTP GET request message  
    int version = 11;  
    http::request<http::empty_body> req{http::verb::get, target, version};  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    //req.keep_alive(true);  
      
    // Send the HTTP request to the remote host  
    http::response<http::string_body> res;  
    beast::error_code ec;  
    do {  
        http::write(socket, req);  
          
        // Receive the HTTP response  
        http::read(socket, buffer, res, ec);  
        if ( ec )  
            if ( ec == beast::http::error::end_of_stream ) {  
                httpclose(socket);  
                buffer.consume(buffer.size());  
                socket = httpconnect(host, "80");  
            } else  
                throw beast::system_error{ec};  
          
    } while (ec);  
      
    return res;  
}  
```

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-02-04 16:43:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460319781  

No idea, what's the error?

---

## Comment 11

> Username: MaximNM  
> Created at: 2019-02-04 16:52:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460323371  

it's terminated at error_code.hpp in line *this = make_error_code(val):  
  
```  
  template<typename ErrorCodeEnum>  
    typename boost::enable_if<is_error_code_enum<ErrorCodeEnum>, error_code>::type &  
      operator=( ErrorCodeEnum val ) BOOST_SYSTEM_NOEXCEPT  
  {  
    *this = make_error_code(val); // !!! HERE !!!  
    return *this;  
  }  
```  
  
with Thread 1: EXC_BAD_ACCESS (code=1, address=0x7ffe00000071)

---

## Comment 12

> Username: vinniefalco  
> Created at: 2019-02-04 16:54:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460324287  

Well, have you tried using a debugger? Inspect the stack? Set a breakpoint when EXC_BAD_ACCESS occurs, and look up the stack...

---

## Comment 13

> Username: MaximNM  
> Created at: 2019-02-04 17:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460338681  

Please see below:  
```  
HTTP Response Status: OK  
HTTP Request: / 301  
(lldb) bt  
* thread #1, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=EXC_I386_GPFLT)  
  * frame #0: 0x0000000100012eb3 httpsync`boost::enable_if<boost::system::is_error_code_enum<boost::asio::error::basic_errors>, boost::system::error_code>::type& boost::system::error_code::operator=<boost::asio::error::basic_errors>(this=0x203a72657672656b, val=operation_aborted) at error_code.hpp:659  
    frame #1: 0x0000000100012ad3 httpsync`boost::asio::detail::kqueue_reactor::deregister_descriptor(this=0x0000000000000000, descriptor=9, descriptor_data=0x00007ffeefbff420, closing=true) at kqueue_reactor.ipp:316  
    frame #2: 0x0000000100012843 httpsync`boost::asio::detail::reactive_socket_service_base::destroy(this=0x00000001011008d8, impl=0x00007ffeefbff418) at reactive_socket_service_base.ipp:89  
    frame #3: 0x000000010005c6e5 httpsync`boost::asio::detail::reactive_socket_service_base::base_move_assign(this=0x00000001011008d8, impl=0x00007ffeefbff418, other_service=0x0000000101100768, other_impl=0x00007ffeefbfedc0) at reactive_socket_service_base.ipp:69  
    frame #4: 0x000000010005c687 httpsync`boost::asio::detail::reactive_socket_service<boost::asio::ip::tcp>::move_assign(this=0x00000001011008b0, impl=0x00007ffeefbff418, other_service=0x0000000101100768, other_impl=0x00007ffeefbfedc0) at reactive_socket_service.hpp:104  
    frame #5: 0x000000010005c628 httpsync`boost::asio::basic_io_object<boost::asio::detail::reactive_socket_service<boost::asio::ip::tcp>, true>::operator=(this=0x00007ffeefbff410, other=0x00007ffeefbfedb8) at basic_io_object.hpp:252  
    frame #6: 0x000000010005c5cf httpsync`boost::asio::basic_socket<boost::asio::ip::tcp>::operator=(this=0x00007ffeefbff410, other=0x00007ffeefbfedb8) at basic_socket.hpp:196  
    frame #7: 0x00000001000043af httpsync`boost::asio::basic_stream_socket<boost::asio::ip::tcp>::operator=(this=0x00007ffeefbff410, other=0x00007ffeefbfedb8) at basic_stream_socket.hpp:169  
    frame #8: 0x0000000100004184 httpsync`httprequest(socket=0x00007ffeefbff410, buffer=0x00007ffeefbff3c8, host="httpbin.org", target="/") at main.cpp:163  
    frame #9: 0x00000001000048dd httpsync`main(argc=1, argv=0x00007ffeefbff598) at main.cpp:200  
    frame #10: 0x00007fff7da89ed9 libdyld.dylib`start + 1  
    frame #11: 0x00007fff7da89ed9 libdyld.dylib`start + 1  
(lldb)   
```

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-02-04 17:42:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460341855  

From your stack it looks like this line is causing the problem:  
```  
socket = httpconnect(host, "80");  
```  
  
This is an Asio issue, not a Beast issue. And I strongly believe the problem is in your code (not Asio).

---

## Comment 15

> Username: MaximNM  
> Created at: 2019-02-04 19:03:18 UTC  
> Updated at: 2019-02-04 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-460370274  

I suppose that I found the issue. The net::io_context has to be alive during program runtime but I destroy it in httpconnect function as it is temporary object.  
  
Code below resolved it.  
  
```  
class httpstream {  
    std::string host;  
    std::string port;  
    net::io_context ioc;  
    tcp::socket socket{ioc};  
    beast::flat_buffer buffer;  
      
public:  
    httpstream() {};  
    virtual ~httpstream() { close(); };  
      
    httpstream(const httpstream&) = delete;  
    httpstream& operator= (const httpstream&) = delete;  
      
    httpstream(httpstream&) = default;  
    httpstream& operator= (httpstream&&) = default;  
      
    void connect(const std::string& host, const std::string& port);  
    void connect() { connect(host, port); };  
    auto request(const std::string& target);  
      
    bool is_open() { return socket.is_open(); };  
      
    void close();  
};  
  
void httpstream::connect(const std::string& host, const std::string& port)  
{  
    this->host = host;  
    this->port = port;  
      
    // These objects perform our I/O  
    tcp::resolver resolver{ioc};  
      
    // Look up the domain name  
    auto const results = resolver.resolve(host, port);  
      
    // Make the connection on the IP address we get from a lookup  
    net::connect(socket, results.begin(), results.end());  
}  
  
void httpstream::close()  
{  
    // Gracefully close the socket  
    beast::error_code ec;  
    socket.shutdown(tcp::socket::shutdown_both, ec);  
      
    // not_connected happens sometimes  
    // so don't bother reporting it.  
    //  
    if(ec && ec != beast::errc::not_connected)  
        throw beast::system_error{ec};  
      
    // If we get here then the connection is closed gracefully  
}  
  
auto httpstream::request(const std::string& target)  
{  
    // Set up an HTTP GET request message  
    int version = 11;  
    http::request<http::empty_body> req{http::verb::get, target, version};  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    //req.keep_alive(true);  
      
    // Send the HTTP request to the remote host  
    http::response<http::string_body> res;  
    beast::error_code ec;  
    do {  
        http::write(socket, req);  
          
        // Receive the HTTP response  
        http::read(socket, this->buffer, res, ec);  
        if ( ec )  
            if ( ec == beast::http::error::end_of_stream ) {  
                close();  
                buffer.consume(buffer.size());  
                connect();  
            } else  
                throw beast::system_error{ec};  
          
    } while (ec);  
      
    return res;  
}  
```

---

## Comment 16

> Username: vinniefalco  
> Created at: 2019-02-08 03:16:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-461677326  

Has this issue been fully resolved?

---

## Comment 17

> Username: MaximNM  
> Created at: 2019-02-08 10:38:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-461761302  

Yes Thanks!  
Just one question concerning SSL_SHORT_READ issue.  
I found a way how to catch it in example code:  
```  
if ((error.category() == boost::asio::error::get_ssl_category())  
     && (ERR_GET_REASON(error.value()) == SSL_R_SHORT_READ))  
{  
  // Remote peer failed to send a close_notify message.  
}  
```  
  
But it doesn't compile on ubuntu I got following error: "'SSL_R_SHORT_READ' was not declared in this scope"  
  
I found a workaround:  
```  
if (ec == net::error::eof || ((ec.category() == boost::asio::error::get_ssl_category()) && (ec.value() == boost::asio::ssl::error::stream_truncated))) {  
  
}  
```  
What do you think it is correct way to handle the error?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2019-02-08 12:15:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-461784656  

You can ignore short reads if you do not have a partial HTTP message.

---

## Comment 19

> Username: MaximNM  
> Created at: 2019-02-08 12:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-461793161  

Indeed but what do I have to do if I'm using https?

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-02-08 13:10:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-461797794  

HTTP and HTTPS messages are the same...

---

## Comment 21

> Username: MaximNM  
> Created at: 2019-02-09 16:43:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-462059484  

Vinnie,  
  
Is it possible to reuse ssl::stream for another connection like below?  
```  
auto httpsstream::request(const std::string& target)  
{  
    // Set up an HTTP GET request message  
    http::request<http::empty_body> req{http::verb::get, target, 11};  
    req.set(http::field::host, host_);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    //req.keep_alive(true);  
      
    // Send the HTTP request to the remote host  
    http::response<http::string_body> res;  
    beast::error_code ec;  
    do {  
        http::write(stream_, req);  
          
        // Receive the HTTPS response  
        http::read(stream_, buffer_, res, ec);  
        if (ec)  
            if (ec == beast::http::error::end_of_stream || ((ec.category() == boost::asio::error::get_ssl_category()) && (ec.value() == boost::asio::ssl::error::stream_truncated))) {  
                std::cerr << "httpsstream request error: " << ec.message() << " -> reconnect\n";  
                close();  
                buffer_.consume(buffer_.size());  
                connect();  
            } else  
                throw beast::system_error{ec};  
          
    } while (ec);  
      
    return res;  
}  
```  
  
I faced with strange case when I'm using the code on macOS and clang it works but when I'm using the code on ubuntu 18.10 and gcc I got error in line "http::write(stream_, req);" - "Exception: protocol is shutdown" it means the app can connect to an end point but cannot write to the stream?

---

## Comment 22

> Username: vinniefalco  
> Created at: 2019-02-09 16:59:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-462060755  

No idea really, it comes down to `asio::ssl::stream` not Beast. If you want to "reuse" the stream you could store it in a `std::optional` (or `boost::optional`) and reconstruct it with `optional::emplace` as needed.

---

## Comment 23

> Username: stale[bot]  
> Created at: 2019-03-11 17:19:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-471636536  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 24

> Username: MaximNM  
> Created at: 2019-03-12 00:05:39 UTC  
> Updated at: 2019-03-12 00:06:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-471790454  

I found a work around how to reuse https socket.  
I'm using SSL_clear(stream_.native_handle()) please see here - https://stackoverflow.com/questions/50693708/boost-asio-ssl-not-able-to-receive-data-for-2nd-time-onwards-1st-time-ok  
I suppose it's a boost::asio::ssl::stream<> issue.  
May be it helps somebody.  
  
```  
void httpsstream::close()  
{  
    // Gracefully close the stream  
    beast::error_code ec;  
    stream_.shutdown(ec);  
    if (ec == net::error::eof || ((ec.category() == net::error::get_ssl_category()) && (ec.value() == ssl::error::stream_truncated))) {  
        // Rationale:  
        // http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error  
        std::cerr << "httpsstream::close error: " << ec.message() << std::endl;  
        ec.assign(0, ec.category());  
    }  
    if (ec)  
        throw beast::system_error{ec};  
    // If we get here then the connection is closed gracefully  
      
    //https://stackoverflow.com/questions/50693708/boost-asio-ssl-not-able-to-receive-data-for-2nd-time-onwards-1st-time-ok  
    SSL_clear(stream_.native_handle());  
    buffer_.consume(buffer_.size());  
    //stream_.reset();  
}  
```

---

## Comment 25

> Username: MaximNM  
> Created at: 2019-03-12 00:10:16 UTC  
> Updated at: 2019-03-12 00:10:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-471791434  

May be somebody knows how to identify a stream truncated error on linux.  
I try - ```((ec.category() == net::error::get_ssl_category()) && (ec.value() == ssl::error::stream_truncated))```  
It's working on macOS but not on linux :(

---

## Comment 26

> Username: stale[bot]  
> Created at: 2019-04-11 00:39:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-481921947  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 27

> Username: stale[bot]  
> Created at: 2019-04-18 00:44:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1429#issuecomment-484313077  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
