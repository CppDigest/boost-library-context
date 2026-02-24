# #1912 - read Operation canceled and read partial message for 2 continuous http::async_write() [Closed]

> Username: luo4neck  
> Created at: 2020-04-22 11:24:38 UTC  
> Updated at: 2020-04-23 17:45:17 UTC  
> Closed at: 2020-04-23 17:45:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1912  

BOOST_BEAST_VERSION 255  
  
Basically I composed this [http_client_async_ssl sample](https://github.com/boostorg/beast/blob/develop/example/http/client/async-ssl/http_client_async_ssl.cpp) and Vinnie's [sample code with queue](https://github.com/boostorg/beast/issues/1381#issuecomment-449392440)   
  
But when I tried to send 2 requests, both of them failed like this:  
```  
scripts$ read: Operation canceled  
scripts$  
scripts$ read: partial message  
```  
  
When I sent only 1 request, it succeeded.   
  
When I tried to `sleep(2)` for 2 seconds between the 2 requests, both succeeded.   
  
Not sure if it is because the type I defined `using tcp_stream_tp = beast::basic_stream<net::ip::tcp, net::strand<net::io_context::executor_type>, beast::unlimited_rate_policy>`?   
  
Http code:  
```  
#include "root_certificates.h"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/strand.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <queue>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
struct HttpReq  
{  
    int method; // 0 for get, 1 for post..  
    string query;  
};  
  
// Report a failure  
void fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
using tcp_stream_tp = beast::basic_stream<net::ip::tcp, net::strand<net::io_context::executor_type>, beast::unlimited_rate_policy>;  
  
// Performs an HTTP GET and prints the response  
class HttpsAsyncClient : public std::enable_shared_from_this<HttpsAsyncClient>  
{  
private:  
    tcp::resolver _resolver;  
    beast::ssl_stream<tcp_stream_tp> _stream;  
    beast::flat_buffer _buffer; // (Must persist between reads)  
    http::request<http::empty_body> _req_get;  
    http::request<http::empty_body> _req_post;  
    http::response<http::string_body> _res;  
    queue<shared_ptr<HttpReq>> _queue;  
    bool _handshaked = false;  
  
public:  
    explicit  
    HttpsAsyncClient(net::io_context& ioc, ssl::context& ctx)  
            : _resolver(net::make_strand(ioc))  
            , _stream(net::make_strand(ioc), ctx)  
    {}  
  
    bool handshaked()  
    {  
        return _handshaked;  
    }  
  
    // Start the asynchronous operation  
    void run(char const* host, char const* port, int version)  
    {  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if(! SSL_set_tlsext_host_name(_stream.native_handle(), host))  
        {  
            beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
            std::cerr << ec.message() << "\n";  
            return;  
        }  
  
        // Set up an HTTP GET request message  
        _req_get.version(version);  
        _req_get.target("");  
        _req_get.method(http::verb::get);  
        _req_get.set(http::field::host, host);  
        _req_get.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // header..  
        _req_post.version(version);  
        _req_post.target("");  
        _req_post.method(http::verb::post);  
        _req_post.set(http::field::host, host);  
        _req_post.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // Look up the domain name  
        _resolver.async_resolve(  
                host,  
                port,  
                beast::bind_front_handler(&HttpsAsyncClient::on_resolve, shared_from_this()));  
    }  
  
    void on_resolve(beast::error_code ec, tcp::resolver::results_type results)  
    {  
        if(ec)  
            return fail(ec, "resolve");  
  
        // Set a timeout on the operation  
        beast::get_lowest_layer(_stream).expires_after(std::chrono::seconds(30));  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(_stream).async_connect(  
                results,  
                beast::bind_front_handler(&HttpsAsyncClient::on_connect, shared_from_this()));  
    }  
  
    void on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type)  
    {  
        if(ec)  
            return fail(ec, "connect");  
  
        // Perform the SSL handshake  
        _stream.async_handshake(  
                ssl::stream_base::client,  
                beast::bind_front_handler(&HttpsAsyncClient::on_handshake, shared_from_this()));  
    }  
  
    void on_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
  
        cout << "handshaked" << endl;  
  
        _handshaked = true;  
    }  
  
    // Send a message to the remote peer.  
    // May be called from any thread.  
    void send(shared_ptr<HttpReq> httpreq)  
    {  
        if (!_stream.get_executor().running_in_this_thread())  
        {  
            return net::post(  
                    _stream.get_executor(),  
                    beast::bind_front_handler(&HttpsAsyncClient::send, shared_from_this(), httpreq));  
        }  
  
        // Always add to queue  
        _queue.push(httpreq);  
  
        // Are we already writing?  
        if(_queue.size() > 1)  
            return;  
  
        if (_queue.front()->method == 0) // get  
        {  
            cout << "get query -> " << _queue.front()->query << endl;  
            _req_get.target(_queue.front()->query);  
            http::async_write(  
                    _stream,  
                    _req_get,  
                    beast::bind_front_handler(&HttpsAsyncClient::on_write, shared_from_this()));  
  
            _queue.pop();  
        }  
        else // post  
        {  
            cout << "post query -> " << _queue.front()->query << endl;  
            _req_post.target(_queue.front()->query);  
            http::async_write(  
                    _stream,  
                    _req_post,  
                    beast::bind_front_handler(&HttpsAsyncClient::on_write, shared_from_this()));  
  
            _queue.pop();  
        }  
    }  
  
    void on_write(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        // Receive the HTTP response  
        http::async_read(  
                _stream,  
                _buffer,  
                _res,  
                beast::bind_front_handler(&HttpsAsyncClient::on_read, shared_from_this()));  
    }  
  
    void on_read(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "read");  
  
        // Write the message to standard out  
        std::cout << _res << std::endl;  
  
        // write something back to main thread..  
    }  
};   
```  
  
Code which sends the requests:  
```  
        net::io_context _ioc;  
        ssl::context ctx{ssl::context::tlsv12_client};  
        load_root_certificates(ctx);  
        ctx.set_verify_mode(ssl::verify_peer);  
        auto client = std::make_shared<HttpsAsyncClient>(_ioc, ctx);  
        client->run("api.xxx.com", "443", 11);  
  
        auto work = net::make_work_guard(_ioc);  
        thread t{[&](){ _ioc.run(); }};  
  
        cout << "to start main loop" << endl;  
  
        while (!client->handshaked())  
        {  
            sleep(1);  
            cout << "sleep 1 sec" << endl;  
        }  
  
        HttpReq httpreq0;  
        httpreq0.method = 0;  
        httpreq0.query = "/api/v3/time";  
        client->send(make_shared<HttpReq>(httpreq0));  
  
        //sleep(2); // if I dont have this sleep, both send will fail  
  
        HttpReq httpreq1;  
        httpreq1.method = 0;  
        httpreq1.query = "/api/v3/ping";  
        client->send(make_shared<HttpReq>(httpreq1));  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-22 12:08:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1912#issuecomment-617739028  

This code has a data race. You're seeing undefined behaviour because of that.

---

## Comment 2

> Username: luo4neck  
> Created at: 2020-04-22 12:21:24 UTC  
> Updated at: 2020-04-22 12:21:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1912#issuecomment-617745681  

thanks @madmongo1   
Is data race from the `_queue`? If yes I guess use `<boost/lockfree/queue.hpp>` or `<boost/lockfree/spsc_queue.hpp>` could help?   
  
I will have only 1 thread calling the `send`

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-04-22 13:57:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1912#issuecomment-617795196  

You have a race because you're not co-ordinating the beginning of the first asynchronous sequence (send, receive) with the end of the last one.  
  
You are not allowed to have more than one asynchronous write and one asyncronous read in progress at any time on any stream in asio.  
  
If you are going to split this code into 2 threads , you must enqueue the second request until the first is complete.

---

## Comment 4

> Username: luo4neck  
> Created at: 2020-04-22 21:04:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1912#issuecomment-618039722  

@madmongo1 I updated the code and only enqueueing `send` to strand (part of my _stream right?) by `post` now. As far as I know `post` should be thread safe.   
  
`send` `on_write` `on_read` should all be called by `strand` only. Why is it still having problem?   
  
```  
scripts$ read: Operation canceled  
variation_binance_om: /usr/local/include/boost/boost/beast/core/detail/stream_base.hpp:73: boost::beast::detail::stream_base::pending_guard::pending_guard(bool&): Assertion `! b_' failed.  
```  
  
```  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
struct HttpReq  
{  
    int method; // 0 for get, 1 for post..  
    string query;  
};  
  
// Report a failure  
void fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
using tcp_stream_tp = beast::basic_stream<net::ip::tcp, net::strand<net::io_context::executor_type>, beast::unlimited_rate_policy>;  
  
// Performs an HTTP GET and prints the response  
class HttpsAsyncClient : public std::enable_shared_from_this<HttpsAsyncClient>  
{  
private:  
    tcp::resolver _resolver;  
    beast::ssl_stream<tcp_stream_tp> _stream;  
    beast::flat_buffer _buffer; // (Must persist between reads)  
    http::request<http::empty_body> _req_get;  
    http::request<http::empty_body> _req_post;  
    http::response<http::string_body> _res;  
    bool _handshaked = false;  
  
public:  
    explicit  
    HttpsAsyncClient(net::io_context& ioc, ssl::context& ctx)  
            : _resolver(net::make_strand(ioc))  
            , _stream(net::make_strand(ioc), ctx)  
    {}  
  
    bool handshaked()  
    {  
        return _handshaked;  
    }  
  
    // Start the asynchronous operation  
    void run(char const* host, char const* port, int version)  
    {  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if(! SSL_set_tlsext_host_name(_stream.native_handle(), host))  
        {  
            beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
            std::cerr << ec.message() << "\n";  
            return;  
        }  
  
        // Set up an HTTP GET request message  
        _req_get.version(version);  
        _req_get.target("");  
        _req_get.method(http::verb::get);  
        _req_get.set(http::field::host, host);  
        _req_get.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // header..  
        _req_post.version(version);  
        _req_post.target("");  
        _req_post.method(http::verb::post);  
        _req_post.set(http::field::host, host);  
        _req_post.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // Look up the domain name  
        _resolver.async_resolve(  
                host,  
                port,  
                beast::bind_front_handler(&HttpsAsyncClient::on_resolve, shared_from_this()));  
    }  
  
    void on_resolve(beast::error_code ec, tcp::resolver::results_type results)  
    {  
        if (ec)  
            return fail(ec, "resolve");  
  
        // Set a timeout on the operation  
        beast::get_lowest_layer(_stream).expires_after(std::chrono::seconds(30));  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(_stream).async_connect(  
                results,  
                beast::bind_front_handler(&HttpsAsyncClient::on_connect, shared_from_this()));  
    }  
  
    void on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type)  
    {  
        if(ec)  
            return fail(ec, "connect");  
  
        // Perform the SSL handshake  
        _stream.async_handshake(  
                ssl::stream_base::client,  
                beast::bind_front_handler(&HttpsAsyncClient::on_handshake, shared_from_this()));  
    }  
  
    void on_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
  
        cout << "handshaked" << endl;  
  
        _handshaked = true;  
    }  
  
    // Send a message to the remote peer.  
    // May be called from any thread.  
    void enqueue(shared_ptr<HttpReq> httpreq)  
    {  
        return net::post(  
                _stream.get_executor(),  
                beast::bind_front_handler(&HttpsAsyncClient::send, shared_from_this(), httpreq));  
    }  
  
    void send(shared_ptr<HttpReq> httpreq)  
    {  
        if (httpreq->method == 0) // get  
        {  
            cout << "get query -> " << httpreq->query << endl;  
            _req_get.target(httpreq->query);  
  
            // Set a timeout on the operation  
            beast::get_lowest_layer(_stream).expires_after(std::chrono::seconds(30));  
  
            http::async_write(  
                    _stream,  
                    _req_get,  
                    beast::bind_front_handler(&HttpsAsyncClient::on_write, shared_from_this()));  
        }  
        else // post  
        {  
            cout << "post query -> " << httpreq->query << endl;  
            _req_post.target(httpreq->query);  
  
            // Set a timeout on the operation  
            beast::get_lowest_layer(_stream).expires_after(std::chrono::seconds(30));  
  
            http::async_write(  
                    _stream,  
                    _req_post,  
                    beast::bind_front_handler(&HttpsAsyncClient::on_write, shared_from_this()));  
        }  
    }  
  
    void on_write(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if (ec)  
            return fail(ec, "write");  
  
        // Set a timeout on the operation  
        beast::get_lowest_layer(_stream).expires_after(std::chrono::seconds(30));  
  
        // Receive the HTTP response  
        http::async_read(  
                _stream,  
                _buffer,  
                _res,  
                beast::bind_front_handler(&HttpsAsyncClient::on_read, shared_from_this()));  
    }  
  
    void on_read(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if (ec)  
            return fail(ec, "read");  
  
        // Write the message to standard out  
        std::cout << _res << std::endl;  
    }  
};  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-04-22 22:14:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1912#issuecomment-618068302  

A `strand` is not a queue. What will happen is `send` will be called, and then when you call `enqueue` again, `send` will be called again immediately, and this is not allowed because you have to wait for the first `http::async_write` to finish before calling `async_write` again. You need to implement your own queue. An example using websocket is located here:  
https://github.com/boostorg/beast/blob/f5064e0c460664fa34fdad44e2379a2309d39685/example/websocket/server/chat-multi/websocket_session.cpp#L84  
  
You can adapt this to HTTP messages instead.

---

## Comment 6

> Username: luo4neck  
> Created at: 2020-04-23 15:21:44 UTC  
> Updated at: 2020-04-23 15:22:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1912#issuecomment-618460219  

@vinniefalco thanks a lot Vinnie, you are the superhero!  
  
This pattern worked, I got `socket closed because read timeout` issue but fixed by adding a `_read_counter`, I think this approach follows your idea?   
  
```  
class HttpsAsyncClient : public enable_shared_from_this<HttpsAsyncClient>  
{  
private:  
    queue<shared_ptr<HttpReq>> _queue;  
    atomic_int _read_counter = 0; // is lock free, used to count do_read() to perform..  
    bool _handshaked = false;  
  
public:  
    void send(shared_ptr<HttpReq> const& httpreq)  
    {  
        return net::post(  
                _stream.get_executor(),  
                beast::bind_front_handler(&HttpsAsyncClient::on_send, shared_from_this(), httpreq));  
    }  
  
    void on_send(shared_ptr<HttpReq> const& httpreq)  
    {  
        _queue.push(httpreq);  
        _read_counter++;  
  
        if (_queue.size() <= 1)  
            do_write();  
  
        if (_read_counter <= 1)  
            do_read();  
    }  
  
    void do_write()  
    {  
        if (_queue.front()->method == 0) // get  
        {  
            cout << "get query -> " << _queue.front()->query << endl;  
            _req_get.target(_queue.front()->query);  
  
            http::async_write(  
                    _stream,  
                    _req_get,  
                    beast::bind_front_handler(&HttpsAsyncClient::on_write, shared_from_this()));  
        }  
        else // post  
        {  
            cout << "post query -> " << _queue.front()->query << endl;  
            _req_post.target(_queue.front()->query);  
  
            http::async_write(  
                    _stream,  
                    _req_post,  
                    beast::bind_front_handler(&HttpsAsyncClient::on_write, shared_from_this()));  
        }  
    }  
  
    void do_read()  
    {  
        beast::get_lowest_layer(_stream).expires_after(chrono::seconds(30));  
  
        http::async_read(  
                _stream,  
                _buffer,  
                _res,  
                beast::bind_front_handler(&HttpsAsyncClient::on_read, shared_from_this()));  
    }  
  
    void on_write(beast::error_code ec, size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        _queue.pop();  
  
        // Receive the HTTP response  
        if (!_queue.empty())  
            do_write();  
    }  
  
    void on_read(beast::error_code ec, size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "read");  
  
        // Write the message to standard out  
        cout << _res.result() << " " << _res.body() << endl;  
  
        _buffer.consume(_buffer.size());  
        _res.body().clear();  
  
        _read_counter--;  
  
        if (_read_counter > 0)  
            do_read();  
    }  
};   
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-04-23 17:40:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1912#issuecomment-618541950  

much better
