# #927 - Repeated calls to an HTTPS URL. [Closed]

> Username: cogle  
> Created at: 2017-12-08 16:37:09 UTC  
> Updated at: 2024-07-04 23:16:19 UTC  
> Closed at: 2017-12-09 00:45:41 UTC  
> Url: https://github.com/boostorg/beast/issues/927  

Hello Vinnie I have followed your example code for the [http_client_async_ssl](https://github.com/boostorg/beast/blob/develop/example/http/client/async-ssl/http_client_async_ssl.cpp) and am able to get it to work well. I have a question with regard on how to correctly modify this code to make repeated calls to the same HTTPS url and endpoint. Currently in my code I have something like  
```  
void HTTPSClient::OnRead(boost::system::error_code ec, std::size_t bytes_transferred, std::shared_ptr<HTTPSQueryStruct> query)  
{  
  boost::ignore_unused(bytes_transferred);  
  
  if (ec)  
    return ::fail(ec, "read");  
  
  // Write the message to standard out  
  std::cout << query->res.body() << std::endl;  
  
  //Default Connection Info is a shared_ptr that is stored in the class where host Url is the desired   
  //endpoint and port is 443.  
  _resolver.async_resolve(_default_connection_info->host_url.c_str(), _default_connection_info->Port(),  
                          std::bind(&HTTPSClient::OnResolve, shared_from_this(),  
                                    std::placeholders::_1, std::placeholders::_2, query));  
}  
```  
I am wondering if this is the most performant way to do this I had initially tried replacing the last line in the above code with  
  
```  
  http::async_write(query->stream, query->req,  
                    std::bind(&HTTPSClient::OnWrite, shared_from_this(), std::placeholders::_2,   
                     std::placeholders::_2, query));  
```  
  
However, it seems as if the endpoint would close the connection on me. The majority of my code is the same as yours and wondering what the best practices while simultaneously maintaining good performance for making repeated calls to the same URL(the endpoint is the same too).   
  
Here is my HTTPSQueryStruct.  
  
```  
  struct HTTPSQueryStruct  
  {  
    HTTPSQueryStruct(std::shared_ptr<boost::asio::io_context> ioc, ssl::context & c) :  
                     stream(*ioc, c)  
    {  
      running.exchange(true, std::memory_order_relaxed);  
    }  
  
    std::atomic_bool running;      
  
    ssl::stream<tcp::socket> stream;  
    boost::beast::flat_buffer buffer;   
    http::request<http::empty_body> req;  
    http::response<http::string_body> res;  
  };  
```  
  
In addition the `boost::asio::io_context` is kept running with the following so I am certain that the `boost::asio::io_context` is running for the longevity of the program even if there is no immediate work.  
```  
_io_context = _init_info->io_context_ptr;  
_work_context = std::make_unique<boost::asio::executor_work_guard<boost::asio::io_context::executor_type>>(boost::asio::make_work_guard(*_io_context));  
```  
  
Edit: Protocol is  HTTPS/1.1

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-08 17:41:20 UTC  
> Updated at: 2017-12-08 17:42:31 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-350325208  

If you want to issue more than one request on the same connection just send another request and then read the response. This is the "keep-alive" mechanism of HTTP/1.1 and it is on by default. If you get a response from the remote host which indicates the connection will be closed after the response is sent there's nothing you do. You can detect this condition by inspecting the return value from a call to `message::keep_alive` on the response object.  
  
In your example code above, it looks like you are performing a domain name lookup after receiving a response from the remote host? Why are you doing that? It should not be necessary if your intent is to submit another request on the same connection. Doing so will certainly hurt performance.

---

## Comment 2

> Username: cogle  
> Created at: 2017-12-08 17:49:04 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-350327194  

Ok, cool thanks. Just to clarify when you say   
  
> send another request  
  
 you mean just simply after everything is read in the `OnRead` just have a call to   
```  
http::async_write(query->stream, query->req,  
                    std::bind(&HTTPSClient::OnWrite, shared_from_this(), std::placeholders::_1, std::placeholders::_2, query));  
```  
  
Which will then resend the request that I desire, as long as the `message::keep_alive`  field is true?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-08 17:54:14 UTC  
> Updated at: 2017-12-08 17:54:21 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-350328517  

Yes. You will need to adjust the `query->req` to reflect the new request. Fields are not automatically cleared after sending. You can use a `boost::optional` for that:  
```  
boost::optional<http::request<http::string_body>> req;  
```  
Then call `req.emplace()` to create a new, empty message

---

## Comment 4

> Username: cogle  
> Created at: 2017-12-08 18:19:18 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-350334533  

Will close this once I can get home and try this solution out.   
Thanks for your help.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-12-09 01:40:21 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-350413938  

Did it work?

---

## Comment 6

> Username: cogle  
> Created at: 2017-12-09 01:50:27 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-350414706  

Yes it did! Thanks for your help. If you have any minor issues that you think a starter could pick up and work on do not hesitate to ask I would gladly try and help.

---

## Comment 7

> Username: d3roch4  
> Created at: 2018-08-14 13:52:15 UTC  
> Updated at: 2018-08-14 13:56:49 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-412878949  

Dont work for me!  
Erro: read: end of stream  
in cpp:  
  
```c++  
#include "httpcliente.h"  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <d3util/stacktrace.h>  
#include <regex>  
#include <thread>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
using namespace std;  
  
namespace httpserver {  
  
// Report a failure  
void fail(boost::system::error_code ec, char const* what)  
{  
    stringstream ss;  
    ss << what << ": " << ec.message() << "\n";  
    throw_with_trace(runtime_error(ss.str()));  
}  
  
HttpCliente::HttpCliente(const std::string &base_url)  
{  
    this->base_url_ = base_url;  
}  
  
void HttpCliente::connect()  
{  
    regex ex("(http|https)://([^/ :]+):?([^/ ]*)(/?[^ #?]*)\\x3f?([^ #]*)#?([^ ]*)");  
    cmatch what;  
    if( regex_match(base_url_.c_str(), what, ex))  
    {  
        string port{what[3].first, what[3].second};  
        if(port.empty()){  
            string protocol{what[0].first, what[0].second};  
            if(protocol == "http") port = "80";  
            else port = "443";  
        }  
        host_.assign(what[2].first, what[2].second);  
  
        // Look up the domain name  
        resolver_.async_resolve(host_, port, std::bind(  
                &HttpCliente::on_resolve,  
                this,  
                std::placeholders::_1,  
                std::placeholders::_2));  
    }  
}  
  
void HttpCliente::close()  
{  
    boost::system::error_code ec;  
  
    // Gracefully close the socket  
    socket_.shutdown(tcp::socket::shutdown_both, ec);  
  
    // not_connected happens sometimes so don't bother reporting it.  
    if(ec && ec != boost::system::errc::not_connected);  
//        return fail(ec, "shutdown");  
  
    // If we get here then the connection is closed gracefully  
}  
  
void HttpCliente::on_resolve(boost::system::error_code ec, tcp::resolver::results_type results)  
{  
    if(ec)  
        return fail(ec, "resolve");  
  
    // Make the connection on the IP address we get from a lookup  
    boost::asio::async_connect(  
        socket_,  
        results.begin(),  
        results.end(),  
        std::bind(  
            &HttpCliente::on_connect,  
            this,  
            std::placeholders::_1));  
}  
  
void HttpCliente::on_connect(boost::system::error_code ec)  
{  
    if(ec)  
        return fail(ec, "connect");  
    request();  
}  
  
void HttpCliente::request(http::verb method, const string& target, const httpserver::JSONObject& json, std::function<void(http::response<http::string_body>)> callback)  
{  
    const string& content = json.toStyledString();  
    http::request<http::string_body> req{method, target, 11};  
    req.set(http::field::host, host_);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    req.set(http::field::content_length, content.size());  
    req.set(http::field::content_type, "application/json");  
    req.body() = content;  
  
    swap(callback_, callback);  
    swap(req, req_);  
  
    if(socket_.is_open())  
        request();  
    else  
        connect();  
  
    // Run the I/O service.  
    ioc_.reset();  
    ioc_.run();  
}  
  
void HttpCliente::request()  
{  
    // Send the HTTP request to the remote host  
        http::async_write(socket_, req_,  
            std::bind(  
                &HttpCliente::on_write,  
                this,  
                std::placeholders::_1,  
                std::placeholders::_2));  
}  
  
void HttpCliente::on_write(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
  
    if(ec)  
        return fail(ec, "write");  
  
    buffer_.consume(buffer_.capacity());  
  
    http::response<http::string_body> res;  
    // Receive the HTTP response  
    http::async_read(socket_, buffer_, res_,  
        std::bind(  
            &HttpCliente::on_read,  
            this,  
            std::placeholders::_1,  
            std::placeholders::_2));  
}  
  
void HttpCliente::on_read(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
  
    if(ec)  
        return fail(ec, "read");  
  
    callback_(res_);  
  
    if(res_.keep_alive() == false){  
        close();  
    }  
}  
  
  
}  
```  
  
in h:  
```c++  
#ifndef HTTPCLIENTE_H  
#define HTTPCLIENTE_H  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <memory>  
#include <string>  
#include <functional>  
#include <httpserver/parser/json.h>  
  
namespace httpserver {  
  
using tcp = boost::asio::ip::tcp;  
namespace http = boost::beast::http;  
  
class HttpCliente : public std::enable_shared_from_this<HttpCliente>  
{  
    std::string base_url_;  
    std::string host_;  
    // The io_context is required for all I/O  
    boost::asio::io_context ioc_;  
    tcp::resolver resolver_{ioc_};  
    tcp::socket socket_{ioc_};  
    boost::beast::flat_buffer buffer_; // (Must persist between reads)  
    http::response<http::string_body> res_;  
    http::request<http::string_body> req_;  
    std::function<void(http::response<http::string_body>)> callback_;  
  
    void on_resolve(boost::system::error_code ec, tcp::resolver::results_type results);  
    void on_connect(boost::system::error_code ec);  
    void on_write(boost::system::error_code ec, std::size_t bytes_transferred);  
    void on_read(boost::system::error_code ec, std::size_t bytes_transferred);  
    void request();  
public:  
    HttpCliente(const std::string& base_url);  
    void connect();  
    void close();  
    void request(http::verb method, const string& target, const httpserver::JSONObject& json, std::function<void(http::response<http::string_body>)> callback);  
};  
  
}  
#endif // HTTPCLIENTE_H  
```  
  
first request is OK  
second request FAIL

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-08-14 13:56:15 UTC  
> Updated at: 2018-08-14 13:57:00 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-412880293  

> end of stream  
  
`beast::http::error::end_of_stream` is returned when the remote peer closes the connection normally:  
  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__error.html  
_"This error is returned when attempting to read HTTP data, and the stream returns the error boost::asio::error::eof before any octets corresponding to a new HTTP message have been received."_

---

## Comment 9

> Username: d3roch4  
> Created at: 2018-08-14 14:05:20 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-412883554  

So I do not know how to solve!  
  
How can I make this check if I can read it?  
  
Call `ioc.reset()` after `ioc.run()` is correct?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-08-14 14:09:40 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-412885009  

```  
void fail(boost::system::error_code ec, char const* what)  
{  
    if(ec == boost::beast::http::error::end_of_stream)  
        return;  
    ...  
```

---

## Comment 11

> Username: d3roch4  
> Created at: 2018-08-14 14:51:50 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-412899809  

now on the third call the error is:  
write: Broken pipe  
  
but if I always call the `connect` before all the requests always work. Is it necessary to reset the buffer, parser, io_context, or socket between each request?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-08-14 15:05:27 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-412904663  

Yes, you have to reset the parser. The easiest way to do that is to store it in a `boost::optional` or `std::optional` and call `emplace` on it each time you want a new one.

---

## Comment 13

> Username: jayden717  
> Created at: 2020-08-19 13:07:55 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-676327352  

> Yes, you have to reset the parser. The easiest way to do that is to store it in a `boost::optional` or `std::optional` and call `emplace` on it each time you want a new one.  
  
hi vinniefalco，i has the same problem, can you show me the code how to do that for d3roch4's demo code? thanks!

---

## Comment 14

> Username: mitsubishirgb  
> Created at: 2024-07-04 19:03:02 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-2209467124  

i am trying to make multiple http requests through a single tcp connection using multiple coroutines for each request function which i can write successfully i also configured the (keep-alive) header. tcp is good i checked it from wireshark but for some reason the program stops after receiving the first http response from the server :  
  
`#pragma once  
  
#include <boost/asio.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/use_awaitable.hpp>  
#include <iostream>  
#include <atomic>  
  
using boost::asio::ip::tcp;  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
void fail(boost::system::error_code ec, char const* what) {  
    if (ec == http::error::end_of_stream) {  
        std::cerr << "End of stream" << std::endl;  
        return;  
    }  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
class HttpClient {  
public:  
    static std::atomic<size_t> success_count;  
    static asio::ssl::context ssl_context;  
  
    HttpClient(asio::io_context& io_context, const std::string& host, const std::string& port)  
        : io_context(io_context), resolver(io_context), host(host), port(port) {}  
  
    void set_request(const http::request<http::string_body>& req) {  
        this->req = req;  
    }  
  
    asio::awaitable<void> request(asio::ssl::stream<tcp::socket>& socket) {  
        boost::system::error_code ec;  
        co_await send_request(ec, socket);  
        if (ec) {  
            fail(ec, "send_request");  
            co_return;  
        }  
        co_await receive_response(ec, socket);  
        if (ec) {  
            fail(ec, "receive_response");  
            co_return;  
        }  
        co_return;  
    }  
  
    static asio::ssl::stream<tcp::socket> session(asio::io_context& io_context, const std::string& host, const std::string& port) {  
        tcp::resolver resolver(io_context);  
        asio::ssl::context ssl_context{ asio::ssl::context_base::sslv23 };  
        asio::ssl::stream<tcp::socket> socket(io_context, ssl_context);  
        auto endpoints = resolver.resolve(host, port);  
        std::cout << "Connecting" << std::endl;  
        asio::connect(socket.lowest_layer(), endpoints);  
        std::cout << "Handshaking" << std::endl;  
        socket.handshake(asio::ssl::stream_base::client);  
        return socket;  
    }  
  
private:  
    asio::awaitable<void> send_request(boost::system::error_code& ec, asio::ssl::stream<tcp::socket>& socket) {  
        std::cout << "Writing" << std::endl;  
        co_await http::async_write(socket, req, asio::redirect_error(asio::use_awaitable, ec));  
    }  
  
    asio::awaitable<void> receive_response(boost::system::error_code& ec, asio::ssl::stream<tcp::socket>& socket) {  
        beast::flat_buffer buffer;  
        http::response<http::string_body> res;  
        std::cout << "Reading response" << std::endl;  
        co_await http::async_read(socket, buffer, res, asio::redirect_error(asio::use_awaitable, ec));  
        if (!ec) {  
            std::cout << (socket.lowest_layer().is_open() ? "Socket is open after reading" : "Socket is closed after reading") << std::endl;  
            if (res.result() == http::status::ok) {  
                ++success_count;  
                std::cout << "Response: " << res.body() << std::endl;  
            }  
            else {  
                std::cerr << "Response returned with status code: " << res.result_int() << '\n';  
            }  
        }  
    }  
  
    asio::io_context& io_context;  
    tcp::resolver resolver;  
    const std::string& host;  
    const std::string& port;  
    http::request<http::string_body> req;  
};  
std::atomic<size_t> HttpClient::success_count{ 0 };`

---

## Comment 15

> Username: mitsubishirgb  
> Created at: 2024-07-04 19:05:02 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-2209468606  

source file :  
`#include <boost/asio.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/use_awaitable.hpp>  
#include <boost/asio/spawn.hpp>  
#include <iostream>  
#include <string>  
#include "HttpClient.hpp"  
  
using boost::asio::ip::tcp;  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
int main() {  
    try {  
        const std::string host = "httpbin.org";  
        const std::string port = "443"; // HTTPS port  
        const std::string target = "/ip";  
        const size_t num_requests = 5;  
  
        asio::io_context io_context;  
  
        http::request<http::string_body> req{ http::verb::get, target, 11 };  
        req.set(http::field::host, host);  
        req.set(http::field::user_agent, "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36");  
        req.set(http::field::connection, "keep-alive");  
  
        HttpClient client(io_context, host, port);  
        client.set_request(req);  
        auto socket = HttpClient::session(io_context, host, port);  
  
        for (size_t i = 0; i < num_requests; ++i) {  
            asio::co_spawn(io_context, client.request(socket), asio::detached);  
        }  
  
        io_context.run();  
        std::cout << "Number of successful requests: " << HttpClient::success_count.load() << std::endl;  
    }  
    catch (const std::exception& e) {  
        std::cerr << "Main error: " << e.what() << std::endl;  
        return 1;  
    }  
    return 0;  
}`

---

## Comment 16

> Username: ashtum  
> Created at: 2024-07-04 19:25:44 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-2209483999  

@mitsubishirgb, you can't call `http::async_write` before the previous call finishes.  
  
Here you are launching multiple concurrent coroutines, with each one trying to call `http::async_write`:  
```C++  
    for (size_t i = 0; i < num_requests; ++i) {  
        asio::co_spawn(io_context, client.request(socket), asio::detached);  
    }  
```  
You must wait until the current `http::async_write` operation completes before initiating another one.

---

## Comment 17

> Username: mitsubishirgb  
> Created at: 2024-07-04 23:16:18 UTC  
> Url: https://github.com/boostorg/beast/issues/927#issuecomment-2209634554  

> @mitsubishirgb, you can't call `http::async_write` before the previous call finishes.  
>   
> Here you are launching multiple concurrent coroutines, with each one trying to call `http::async_write`:  
>   
> ```c++  
>     for (size_t i = 0; i < num_requests; ++i) {  
>         asio::co_spawn(io_context, client.request(socket), asio::detached);  
>     }  
> ```  
>   
> You must wait until the current `http::async_write` operation completes before initiating another one.  
  
oh my bad i guess , this worked when i created tcp for each async_write now it makes sense thanks
