# #1256 - using std::promise to get a value into async function [Closed]

> Username: Ludea  
> Created at: 2018-09-25 13:20:15 UTC  
> Updated at: 2018-11-28 02:10:47 UTC  
> Closed at: 2018-11-28 02:10:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1256  

Hello,  
I use the [async http server ssl](https://www.boost.org/doc/libs/1_66_0/libs/beast/example/http/server/async-ssl/http_server_async_ssl.cpp)  
But I need to get req value in `do_read()` function into main.  
So I must wait async function be finished.  
I think I had to use std::promise, but I have a lot of async funtions  
I don't really know how to use it.  
  
```  
void request::run(char const* host, char const* port, char const* target, int version)  
{  
    // Set SNI Hostname (many hosts need this to handshake successfully)  
    if(! SSL_set_tlsext_host_name(stream.native_handle(), host))  
    {  
        boost::system::error_code ec{static_cast<int>(::ERR_get_error()), boost::asio::error::get_ssl_category()};  
        std::cerr << ec.message() << "\n";  
        respond = "test" ;  
        return;  
    }  
    // Set up an HTTP GET request message  
    req.version(version);  
    req.method(http::verb::get);  
    req.target(target);  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    req.set(http::field::content_type, "application/x-www-form-urlencoded");  
    req.body() = body.toStdString() ;  
    req.prepare_payload();  
    // Look up the domain name  
    resolver.async_resolve(host, port, std::bind(&request::on_resolve, shared_from_this(), std::placeholders::_1, std::placeholders::_2));  
 }  
void request::on_resolve(boost::system::error_code ec, tcp::resolver::results_type results)  
{  
    if(ec){  
        //return fail(ec, "resolve");  
    }  
    // Make the connection on the IP address we get from a lookup  
    boost::asio::async_connect(stream.next_layer(), results.begin(), results.end(), std::bind( &request::on_connect, shared_from_this(), std::placeholders::_1));  
}  
void request::on_connect(boost::system::error_code ec)  
{  
    if(ec){  
        //return fail(ec, "connect");  
         respond = ec.message();  
        }  
    // Perform the SSL handshake  
    stream.async_handshake(ssl::stream_base::client, std::bind(&request::on_handshake, shared_from_this(), std::placeholders::_1));  
}  
void request::on_handshake(boost::system::error_code ec)  
{  
    if(ec){  
        return fail(ec, "handshake");  
    }  
    // Send the HTTP request to the remote host  
    http::async_write(stream, req, std::bind(&request::on_write, shared_from_this(), std::placeholders::_1, std::placeholders::_2));  
}  
void request::on_write(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
    if(ec)  
        return fail(ec, "write");  
    // Receive the HTTP response  
    future = http::async_read(stream, buffer, res, std::bind(&request::on_read, shared_from_this(), std::placeholders::_1, std::placeholders::_2));  
}  
void request::on_read(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
    if(ec)  
        return fail(ec, "read");  
    // Gracefully close the stream  
    stream.async_shutdown(std::bind(&request::on_shutdown, shared_from_this(), std::placeholders::_1));  
}  
void request::on_shutdown(boost::system::error_code ec)  
{  
    if(ec == boost::asio::error::eof)  
    {  
        ec.assign(0, ec.category());  
    }  
    if(ec)  
      return fail(ec, "shutdown");  
    // If we get here then the connection is closed gracefully  
}  
void request::start_request(QString host, QString port, QString target, int version)  
{  
    boost::asio::io_context ioc;  
    ssl::context ctx{ssl::context::sslv23_client};  
    const char* std_host = host.toStdString().c_str();  
    const char* std_port = port.toStdString().c_str();  
    const char* std_target = target.toStdString().c_str();  
    const char* std_method = method.toStdString().c_str();  
    const char* std_body = body.toStdString().c_str();  
    std::make_shared<request>(ioc, ctx)->run(std_host, std_port, std_target, version, std_method, std_body );  
    ioc.run();  
}  
```  
I think I must start to `start_request()`, and wait all async function finised  
```  
std::future<void> future = run(std_host, std_port, std_target, version);  
future.get();  
```  
but run is not an async function

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-25 13:26:54 UTC  
> Updated at: 2018-09-25 13:27:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1256#issuecomment-424342700  

Users of Beast are expected to already have an understanding of Boost.Asio, in particular how to use it in asynchronous contexts:  
  
**Using futures with boost::asio**  
https://stackoverflow.com/questions/17282434/using-futures-with-boostasio  
  
**Futures**  
https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/overview/cpp2011/futures.html  
  
`use_future_t`  
https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/reference/use_future_t.html  
  
daytime_client.cpp  
https://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/example/cpp11/futures/daytime_client.cpp  
  
**Async usage of futures and promises in ASIO**  
http://code-slim-jim.blogspot.com/2014/07/async-usage-of-futures-and-promises-in.html  
  
**asio::use_future and event loop**  
https://stackoverflow.com/questions/35710080/asiouse-future-and-event-loop

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-11-24 13:40:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1256#issuecomment-441368643  

This issue has been open for a while with no activity, has it been resolved?
