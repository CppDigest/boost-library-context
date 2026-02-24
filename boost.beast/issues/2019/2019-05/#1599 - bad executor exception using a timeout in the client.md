# #1599 - bad executor exception using a timeout in the client [Closed]

> Username: Ban44n  
> Created at: 2019-05-03 10:53:52 UTC  
> Updated at: 2025-12-12 17:28:39 UTC  
> Closed at: 2019-05-05 12:05:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1599  

Hi,  
  
We are using boost beast in a high performance manner, i.e. we need to be able to communicate >1000 transactions per second per connection. For our application it is crucial to use a timeout on the stream which is provided by your library.   
  
We encounter a critical exception when a client has a timeout policy enabled (which sends ping/pong messages by the beast library) and when we stress the async_write function (see example client below) by sending messages all the time. The bug results in an unhandled exception:  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::asio::bad_executor>'  
  what():  bad executor  
Aborted (core dumped)  
```  
  
Stack trace:  
```  
__GI_raise(int sig) (/build/glibc-KRRWSm/glibc-2.29/sysdeps/unix/sysv/linux/raise.c:50)  
__GI_abort() (/build/glibc-KRRWSm/glibc-2.29/stdlib/abort.c:79)  
[Unknown/Just-In-Time compiled code] (Unknown Source:0)  
std::terminate() (Unknown Source:0)  
__cxa_throw (Unknown Source:0)  
boost::throw_exception<boost::asio::bad_executor>(const boost::asio::bad_executor & e) (/usr/local/include/boost/throw_exception.hpp:70)  
boost::asio::executor::get_impl(const boost::asio::executor * const this) (/usr/local/include/boost/asio/executor.hpp:309)  
boost::asio::executor::post<boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor> >, std::allocator<void> >(const boost::asio::executor * const this, boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> > >::idle_ping_op<boost::asio::executor> > && f, const std::allocator<void> & a) (/usr/local/include/boost/asio/impl/executor.hpp:358)  
boost::asio::detail::initiate_post::operator()<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor>, boost::asio::executor const&>(const boost::asio::detail::initiate_post * const this, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor> && handler, const boost::asio::executor & ex) (/usr/local/include/boost/asio/impl/post.hpp:54)  
boost::asio::async_result<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor>, void ()>::initiate<boost::asio::detail::initiate_post, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor>, boost::asio::executor const&>(boost::asio::detail::initiate_post&&, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor>&&, boost::asio::executor const&)(boost::asio::detail::initiate_post && initiation, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor> && token,  args#0) (/usr/local/include/boost/asio/async_result.hpp:82)  
boost::asio::async_initiate<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor>, void (), boost::asio::detail::initiate_post, boost::asio::executor const&>(boost::asio::detail::initiate_post&&, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor>&, boost::asio::executor const&)(boost::asio::detail::initiate_post && initiation, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor> & token,  args#0) (/usr/local/include/boost/asio/async_result.hpp:257)  
boost::asio::post<boost::asio::executor, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor> >(const boost::asio::executor & ex, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor> && token) (/usr/local/include/boost/asio/impl/post.hpp:74)  
boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor>::operator()(boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::executor> * const this, boost::beast::error_code ec, std::size_t bytes_transferred) (/usr/local/include/boost/beast/websocket/impl/ping.hpp:179)  
....  
```  
  
It seems that op_idle_ping implementation in the beast library cannot find the strand to execute the ping operation and an exception is raised (see websocket/impl/ping.hpp:179, I am not sure though). It is our belief that the library should function perfectly fine when the async_write function is properly stressed like our example.  
  
### Version of Beast  
#define BOOST_BEAST_VERSION 248  
(using boost 1_70_00)  
  
### Steps necessary to reproduce the problem  
We have adjusted the client websocket async c++ example to reproduce the bug. Basically we added the timeout function with ping enabled and we keep on sending the message on the stream in a proper way.  
  
```  
//  
// Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: WebSocket SSL client, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include "certificates.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/strand.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void fail(beast::error_code ec, char const *what)  
{  
  std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Sends a WebSocket message and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
  tcp::resolver resolver_;  
  websocket::stream<  
      beast::ssl_stream<beast::tcp_stream>>  
      ws_;  
  beast::flat_buffer buffer_;  
  std::string host_;  
  std::string text_;  
  
public:  
  // Resolver and socket require an io_context  
  explicit session(net::io_context &ioc, ssl::context &ctx)  
      : resolver_(net::make_strand(ioc)), ws_(net::make_strand(ioc), ctx)  
  {  
  }  
  
  // Start the asynchronous operation  
  void  
  run(  
      char const *host,  
      char const *port,  
      char const *text)  
  {  
    // Save these for later  
    host_ = host;  
    text_ = text;  
  
    // Look up the domain name  
    resolver_.async_resolve(  
        host,  
        port,  
        beast::bind_front_handler(  
            &session::on_resolve,  
            shared_from_this()));  
  }  
  
  void  
  on_resolve(  
      beast::error_code ec,  
      tcp::resolver::results_type results)  
  {  
    if (ec)  
      return fail(ec, "resolve");  
  
    // Set a timeout on the operation  
    beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
    // Make the connection on the IP address we get from a lookup  
    beast::get_lowest_layer(ws_).async_connect(  
        results,  
        beast::bind_front_handler(  
            &session::on_connect,  
            shared_from_this()));  
  }  
  
  void  
  on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type)  
  {  
    if (ec)  
      return fail(ec, "connect");  
  
    // Set a timeout on the operation  
    beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
    // Perform the SSL handshake  
    ws_.next_layer().async_handshake(  
        ssl::stream_base::client,  
        beast::bind_front_handler(  
            &session::on_ssl_handshake,  
            shared_from_this()));  
  }  
  
  void  
  on_ssl_handshake(beast::error_code ec)  
  {  
    if (ec)  
      return fail(ec, "ssl_handshake");  
  
    // Turn off the timeout on the tcp_stream, because  
    // the websocket stream has its own timeout system.  
    beast::get_lowest_layer(ws_).expires_never();  
  
    // Set timeout  
    auto timeout = 3;  
    beast::websocket::stream_base::timeout opt{  
        std::chrono::seconds(timeout), // close timeout  
        std::chrono::seconds(timeout), // idle timeout  
        true};                         // pings enabled (results in weird bug)  
    ws_.set_option(opt);  
  
    // Perform the websocket handshake  
    ws_.async_handshake(host_, "/",  
                        beast::bind_front_handler(  
                            &session::on_handshake,  
                            shared_from_this()));  
  }  
  
  void  
  on_handshake(beast::error_code ec)  
  {  
    if (ec)  
      return fail(ec, "handshake");  
  
    // Start writing to the stream  
    ws_.async_write(  
        net::buffer(text_),  
        beast::bind_front_handler(  
            &session::on_write,  
            shared_from_this()));  
  
    // Start reading from the stream  
    ws_.async_read(  
        buffer_,  
        beast::bind_front_handler(  
            &session::on_read,  
            shared_from_this()));  
  }  
  
  void  
  on_write(  
      beast::error_code ec,  
      std::size_t bytes_transferred)  
  {  
    boost::ignore_unused(bytes_transferred);  
  
    if (ec)  
      return fail(ec, "write");  
  
    // Write again  
    ws_.async_write(  
        net::buffer(text_),  
        beast::bind_front_handler(  
            &session::on_write,  
            shared_from_this()));  
  }  
  
  void  
  on_read(  
      beast::error_code ec,  
      std::size_t bytes_transferred)  
  {  
    boost::ignore_unused(bytes_transferred);  
  
    if (ec)  
      return fail(ec, "read");  
  
    // Clear buffer  
    buffer_.clear();  
  
    // Read again  
    ws_.async_read(  
        buffer_,  
        beast::bind_front_handler(  
            &session::on_read,  
            shared_from_this()));  
  }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char **argv)  
{  
  // Check command line arguments.  
  if (argc != 4)  
  {  
    std::cerr << "Usage: websocket-client-async-ssl <host> <port> <text>\n"  
              << "Example:\n"  
              << "    websocket-client-async-ssl echo.websocket.org 443 \"Hello, world!\"\n";  
    return EXIT_FAILURE;  
  }  
  auto const host = argv[1];  
  auto const port = argv[2];  
  auto const text = argv[3];  
  
  // The io_context is required for all I/O  
  boost::asio::io_context ioc;  
  
  // The SSL context is required, and holds certificates  
  ssl::context ctx{ssl::context::tlsv13};  
  
  // Launch the asynchronous operation  
  
  std::make_shared<session>(ioc, ctx)->run(host, port, text);  
  
  ioc.run();  
  
  return EXIT_SUCCESS;  
}  
```  
  
  
### All relevant compiler information  
We are using ubuntu 19.04 and gnu compiler  
```  
g++ (Ubuntu 8.3.0-6ubuntu1) 8.3.0  
```  
  
The server can be anything, a quick running server in nodejs is for example (using the certificate and key of your example directory):  
```  
const WebSocket = require('ws');  
const fs = require('fs');  
const https = require('https');  
  
let cert = `-----BEGIN CERTIFICATE-----  
MIIDaDCCAlCgAwIBAgIJAO8vBu8i8exWMA0GCSqGSIb3DQEBCwUAMEkxCzAJBgNV  
BAYTAlVTMQswCQYDVQQIDAJDQTEtMCsGA1UEBwwkTG9zIEFuZ2VsZXNPPUJlYXN0  
Q049d3d3LmV4YW1wbGUuY29tMB4XDTE3MDUwMzE4MzkxMloXDTQ0MDkxODE4Mzkx  
MlowSTELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAkNBMS0wKwYDVQQHDCRMb3MgQW5n  
ZWxlc089QmVhc3RDTj13d3cuZXhhbXBsZS5jb20wggEiMA0GCSqGSIb3DQEBAQUA  
A4IBDwAwggEKAoIBAQDJ7BRKFO8fqmsEXw8v9YOVXyrQVsVbjSSGEs4Vzs4cJgcF  
xqGitbnLIrOgiJpRAPLy5MNcAXE1strVGfdEf7xMYSZ/4wOrxUyVw/Ltgsft8m7b  
Fu8TsCzO6XrxpnVtWk506YZ7ToTa5UjHfBi2+pWTxbpN12UhiZNUcrRsqTFW+6fO  
9d7xm5wlaZG8cMdg0cO1bhkz45JSl3wWKIES7t3EfKePZbNlQ5hPy7Pd5JTmdGBp  
yY8anC8u4LPbmgW0/U31PH0rRVfGcBbZsAoQw5Tc5dnb6N2GEIbq3ehSfdDHGnrv  
enu2tOK9Qx6GEzXh3sekZkxcgh+NlIxCNxu//Dk9AgMBAAGjUzBRMB0GA1UdDgQW  
BBTZh0N9Ne1OD7GBGJYz4PNESHuXezAfBgNVHSMEGDAWgBTZh0N9Ne1OD7GBGJYz  
4PNESHuXezAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3DQEBCwUAA4IBAQCmTJVT  
LH5Cru1vXtzb3N9dyolcVH82xFVwPewArchgq+CEkajOU9bnzCqvhM4CryBb4cUs  
gqXWp85hAh55uBOqXb2yyESEleMCJEiVTwm/m26FdONvEGptsiCmF5Gxi0YRtn8N  
V+KhrQaAyLrLdPYI7TrwAOisq2I1cD0mt+xgwuv/654Rl3IhOMx+fKWKJ9qLAiaE  
fQyshjlPP9mYVxWOxqctUdQ8UnsUKKGEUcVrA08i1OAnVKlPFjKBvk+r7jpsTPcr  
9pWXTO9JrYMML7d+XRSZA1n3856OqZDX4403+9FnXCvfcLZLLKTBvwwFgEFGpzjK  
UEVbkhd5qstF6qWK  
-----END CERTIFICATE-----`;  
  
let key = `-----BEGIN PRIVATE KEY-----  
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQDJ7BRKFO8fqmsE  
Xw8v9YOVXyrQVsVbjSSGEs4Vzs4cJgcFxqGitbnLIrOgiJpRAPLy5MNcAXE1strV  
GfdEf7xMYSZ/4wOrxUyVw/Ltgsft8m7bFu8TsCzO6XrxpnVtWk506YZ7ToTa5UjH  
fBi2+pWTxbpN12UhiZNUcrRsqTFW+6fO9d7xm5wlaZG8cMdg0cO1bhkz45JSl3wW  
KIES7t3EfKePZbNlQ5hPy7Pd5JTmdGBpyY8anC8u4LPbmgW0/U31PH0rRVfGcBbZ  
sAoQw5Tc5dnb6N2GEIbq3ehSfdDHGnrvenu2tOK9Qx6GEzXh3sekZkxcgh+NlIxC  
Nxu//Dk9AgMBAAECggEBAK1gV8uETg4SdfE67f9v/5uyK0DYQH1ro4C7hNiUycTB  
oiYDd6YOA4m4MiQVJuuGtRR5+IR3eI1zFRMFSJs4UqYChNwqQGys7CVsKpplQOW+  
1BCqkH2HN/Ix5662Dv3mHJemLCKUON77IJKoq0/xuZ04mc9csykox6grFWB3pjXY  
OEn9U8pt5KNldWfpfAZ7xu9WfyvthGXlhfwKEetOuHfAQv7FF6s25UIEU6Hmnwp9  
VmYp2twfMGdztz/gfFjKOGxf92RG+FMSkyAPq/vhyB7oQWxa+vdBn6BSdsfn27Qs  
bTvXrGe4FYcbuw4WkAKTljZX7TUegkXiwFoSps0jegECgYEA7o5AcRTZVUmmSs8W  
PUHn89UEuDAMFVk7grG1bg8exLQSpugCykcqXt1WNrqB7x6nB+dbVANWNhSmhgCg  
VrV941vbx8ketqZ9YInSbGPWIU/tss3r8Yx2Ct3mQpvpGC6iGHzEc/NHJP8Efvh/  
CcUWmLjLGJYYeP5oNu5cncC3fXUCgYEA2LANATm0A6sFVGe3sSLO9un1brA4zlZE  
Hjd3KOZnMPt73B426qUOcw5B2wIS8GJsUES0P94pKg83oyzmoUV9vJpJLjHA4qmL  
CDAd6CjAmE5ea4dFdZwDDS8F9FntJMdPQJA9vq+JaeS+k7ds3+7oiNe+RUIHR1Sz  
VEAKh3Xw66kCgYB7KO/2Mchesu5qku2tZJhHF4QfP5cNcos511uO3bmJ3ln+16uR  
GRqz7Vu0V6f7dvzPJM/O2QYqV5D9f9dHzN2YgvU9+QSlUeFK9PyxPv3vJt/WP1//  
zf+nbpaRbwLxnCnNsKSQJFpnrE166/pSZfFbmZQpNlyeIuJU8czZGQTifQKBgHXe  
/pQGEZhVNab+bHwdFTxXdDzr+1qyrodJYLaM7uFES9InVXQ6qSuJO+WosSi2QXlA  
hlSfwwCwGnHXAPYFWSp5Owm34tbpp0mi8wHQ+UNgjhgsE2qwnTBUvgZ3zHpPORtD  
23KZBkTmO40bIEyIJ1IZGdWO32q79nkEBTY+v/lRAoGBAI1rbouFYPBrTYQ9kcjt  
1yfu4JF5MvO9JrHQ9tOwkqDmNCWx9xWXbgydsn/eFtuUMULWsG3lNjfst/Esb8ch  
k5cZd6pdJZa4/vhEwrYYSuEjMCnRb0lUsm7TsHxQrUd6Fi/mUuFU/haC0o0chLq7  
pVOUFq5mW8p0zbtfHbjkgxyF  
-----END PRIVATE KEY-----`;  
  
const server = https.createServer({  
  key: key,  
  cert: cert  
});  
const wss = new WebSocket.Server({ server });  
  
  
wss.on('connection', function connection(ws) {  
  ws.on('ping', () => {  
    console.log('received ping');  
  });  
  ws.on('pong', () => {  
    console.log('received pong');  
  });  
  ws.on('message', function incoming(message) {  
    console.log('received: %s', message);  
  });  
});  
  
// Start listen  
server.listen(8080);  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-05-03 12:03:45 UTC  
> Updated at: 2019-05-03 12:15:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489072819  

Thank you for the detailed report. Moved-from executors are supposed to still be usable (according to https://github.com/boostorg/asio/commit/e830f97d55c84a0078e307d8c8817e138f4483f2) but it seems that a moved-from polymorphic executor is no longer usable:  
  
https://github.com/boostorg/asio/blob/db6423c9b1bd5c5ff4f7ea391446c7ac593ce549/include/boost/asio/executor.hpp#L70  
  
If possible, please try doing both of these things:  
  
1. Change your copy of the asio source code for `net::executor` to perform a copy instead of a move on a move-construction. I think you can just delete or comment out the move constructor to achieve this. Then run the program to determine if the issue is still present (I suspect it won't be).  
  
2. Change your code to use a strand instead of the polymorphic executor on the websocket stream, by changing your declaration to:  
```  
websocket::stream<beast::ssl_stream<beast::basic_stream<  
    net::ip::tcp, net::strand<net::io_context::executor_type>>>>ws_;  
```  
Then run the program to determine if the issue is still present (I suspect it won't be).  
  
I believe that either of these changes will resolve your issue. The information provided by attempting these changes will help support our case for a bug in asio.

---

## Comment 2

> Username: Ban44n  
> Created at: 2019-05-03 12:33:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489079838  

Thanks for your quick reply!   
  
I have commented out the move-constructor of net::executor inside my boost files, it seems to be working as I haven't received any error when running for 5 minutes. Did you already submit a bug report to asio?  
  
Your second suggestion has my preference, since this is a fix in my own code instead of inside the boost asio header files. I do not always have write access to these files. Unfortunately when I change the stream template to use the strand executor as you suggest, it leads to a segfault (there is no segfault when the ping feature is disabled):  
  
```  
boost::asio::detail::strand_executor_service::enqueue(const boost::asio::detail::strand_executor_service::implementation_type & impl, boost::asio::detail::scheduler_operation * op) (/usr/local/include/boost/asio/detail/impl/strand_executor_service.ipp:99)  
boost::asio::detail::strand_executor_service::post<boost::asio::io_context::executor_type const, boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > >, std::allocator<void> >(const boost::asio::detail::strand_executor_service::implementation_type & impl, const boost::asio::io_context::executor_type & ex, boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > > && function, const std::allocator<void> & a) (/usr/local/include/boost/asio/detail/impl/strand_executor_service.hpp:147)  
boost::asio::strand<boost::asio::io_context::executor_type>::post<boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > >, std::allocator<void> >(const boost::asio::strand<boost::asio::io_context::executor_type> * const this, boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > > && f, const std::allocator<void> & a) (/usr/local/include/boost/asio/strand.hpp:213)  
boost::asio::detail::initiate_post::operator()<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >, boost::asio::strand<boost::asio::io_context::executor_type> const&>(const boost::asio::detail::initiate_post * const this, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > && handler, const boost::asio::strand<boost::asio::io_context::executor_type> & ex) (/usr/local/include/boost/asio/impl/post.hpp:54)  
boost::asio::async_result<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >, void ()>::initiate<boost::asio::detail::initiate_post, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >, boost::asio::strand<boost::asio::io_context::executor_type> const&>(boost::asio::detail::initiate_post&&, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >&&, boost::asio::strand<boost::asio::io_context::executor_type> const&)(boost::asio::detail::initiate_post && initiation, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > && token,  args#0) (/usr/local/include/boost/asio/async_result.hpp:82)  
boost::asio::async_initiate<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >, void (), boost::asio::detail::initiate_post, boost::asio::strand<boost::asio::io_context::executor_type> const&>(boost::asio::detail::initiate_post&&, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >&, boost::asio::strand<boost::asio::io_context::executor_type> const&)(boost::asio::detail::initiate_post && initiation, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > & token,  args#0) (/usr/local/include/boost/asio/async_result.hpp:257)  
boost::asio::post<boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > >(const boost::asio::strand<boost::asio::io_context::executor_type> & ex, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > && token) (/usr/local/include/boost/asio/impl/post.hpp:74)  
boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >::operator()(boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> > * const this, boost::beast::error_code ec, std::size_t bytes_transferred) (/usr/local/include/boost/beast/websocket/impl/ping.hpp:179)  
boost::beast::saved_handler::impl<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >, std::allocator<void> >::invoke(boost::beast::saved_handler::impl<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::idle_ping_op<boost::asio::strand<boost::asio::io_context::executor_type> >, std::allocator<void> > * const this) (/usr/local/include/boost/beast/core/impl/saved_handler.hpp:91)  
boost::beast::saved_handler::maybe_invoke(boost::beast::saved_handler * const this) (/usr/local/include/boost/beast/core/impl/saved_handler.ipp:69)  
boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1>::operator()(boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> * const this, boost::beast::error_code ec, std::size_t bytes_transferred, bool cont) (/usr/local/include/boost/beast/websocket/impl/write.hpp:429)  
boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> >::operator()(boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > * const this, const boost::system::error_code & ec, std::size_t bytes_transferred, int start) (/usr/local/include/boost/asio/impl/write.hpp:338)  
boost::beast::async_base<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> >, boost::asio::strand<boost::asio::io_context::executor_type>, std::allocator<void> >::complete_now<boost::system::error_code&, unsigned long&>(boost::beast::async_base<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> >, boost::asio::strand<boost::asio::io_context::executor_type>, std::allocator<void> > * const this,  args#0,  args#1) (/usr/local/include/boost/beast/core/async_base.hpp:377)  
boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > >::operator()(boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > * const this, boost::system::error_code ec, std::size_t bytes_transferred) (/usr/local/include/boost/beast/core/impl/flat_stream.hpp:74)  
boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>::call_handler<boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > >(const boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer> * const this, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > & handler, const boost::system::error_code & ec, const std::size_t & bytes_transferred) (/usr/local/include/boost/asio/ssl/detail/write_op.hpp:55)  
boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > >::operator()(boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > * const this, boost::system::error_code ec, std::size_t bytes_transferred, int start) (/usr/local/include/boost/asio/ssl/detail/io.hpp:271)  
boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::mutable_buffer, boost::asio::mutable_buffer const*, boost::asio::detail::transfer_all_t, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > >::operator()(boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::mutable_buffer, boost::asio::mutable_buffer const*, boost::asio::detail::transfer_all_t, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > > * const this, const boost::system::error_code & ec, std::size_t bytes_transferred, int start) (/usr/local/include/boost/asio/impl/write.hpp:338)  
boost::beast::async_base<boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::mutable_buffer, boost::asio::mutable_buffer const*, boost::asio::detail::transfer_all_t, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > >, boost::asio::strand<boost::asio::io_context::executor_type>, std::allocator<void> >::complete_now<boost::system::error_code&, unsigned long&>(boost::beast::async_base<boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::mutable_buffer, boost::asio::mutable_buffer const*, boost::asio::detail::transfer_all_t, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > >, boost::asio::strand<boost::asio::io_context::executor_type>, std::allocator<void> > * const this,  args#0,  args#1) (/usr/local/include/boost/beast/core/async_base.hpp:377)  
boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::const_buffers_1, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::mutable_buffer, boost::asio::mutable_buffer const*, boost::asio::detail::transfer_all_t, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > > >::operator()(boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::const_buffers_1, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::mutable_buffer, boost::asio::mutable_buffer const*, boost::asio::detail::transfer_all_t, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > > > * const this, boost::beast::error_code ec, std::size_t bytes_transferred) (/usr/local/include/boost/beast/core/impl/basic_stream.hpp:400)  
boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::const_buffers_1, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::mutable_buffer, boost::asio::mutable_buffer const*, boost::asio::detail::transfer_all_t, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > > >, boost::system::error_code, unsigned long>::operator()(boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> >::ops::transfer_op<false, boost::asio::const_buffers_1, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::mutable_buffer, boost::asio::mutable_buffer const*, boost::asio::detail::transfer_all_t, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>, boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type>, boost::beast::unlimited_rate_policy> >, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::strand<boost::asio::io_context::executor_type> > > >::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::mutable_buffers_1> > > > > >, boost::system::error_code, unsigned long> * const this) (/usr/local/include/boost/asio/detail/bind_handler.hpp:164)  
...  
```  
  
The stack trace shows that again the segfault is produced by the same ping code (ping.hpp:179) that tries to queue something on the strand.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-05-03 12:45:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489082817  

> Your second suggestion has my preference  
  
Yes of course, but the temporary change to asio has produced useful data :) I am investigating...

---

## Comment 4

> Username: djarek  
> Created at: 2019-05-03 16:17:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489154085  

I'm having trouble reproducing this issue in 19.04, tip of develop, using websocket-server-async-ssl as the server and your snippet as the client. Tried both debug and release builds with no luck. Any hints?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-05-03 16:18:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489154327  

@Ban44n Questions:  
  
1. are you using the exact 1.70 distribution?  
2. what compiler flags are you using (e.g. optimization level)  
3. what are the command line parameters used to launch the client  
  
Thanks

---

## Comment 6

> Username: stenreijers  
> Created at: 2019-05-03 22:55:49 UTC  
> Updated at: 2019-05-03 22:56:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489264566  

@vinniefalco   
> 1. are you using the exact 1.70 distribution?  
  
Yes, boost version.hpp says:  
#define BOOST_VERSION 107000  
#define BOOST_LIB_VERSION "1_70"  
  
> 2. what compiler flags are you using (e.g. optimization level)  
  
No special flags:  
g++ test.cpp -lpthread -lcrypto -lssl   
  
or for debugging:  
g++ test.cpp -lpthread -lcrypto -lssl  -g  
  
We are using:  
Ubuntu:  
Distributor ID:	Ubuntu  
Description:	Ubuntu 19.04  
Release:	19.04  
Codename:	disco  
g++ (Ubuntu 8.3.0-6ubuntu1) 8.3.0  
  
> 3. what are the command line parameters used to launch the client  
  
./client localhost 8080 "test"  
  
@djarek Indeed it does not throw the exception when using the websocket-async-server example out of the box for some unknown reason. We do however randomly see this exception happening in our own beast server implementation. In this implementation, we have used limits on the stream like the simple ratelimiter and we have set a timeout policy. I will try to isolate what parameters in our server code server trigger this bahaviour.  
  
If you want to see the bug directly happening, try use the nodejs example that I passed. This uses a widely used websocket implementation that also obeys the spec and passes all autobahn tests. I have just run this on another machine and it directly throws the error on the client.   
  
Like I said, we do also see it happening with the beast server implementation but we have used many flags on the stream. I will investigate and report back.

---

## Comment 7

> Username: stenreijers  
> Created at: 2019-05-04 10:08:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489313654  

@djarek @vinniefalco   
  
I found out why the async-server example does not reproduce the exception on the client side: the server example echos all messages directly back to the client and therefore the idle_timeout timer on the client never triggers. The error can be easily reproduced by putting the server-example in read-only mode.  
  
The full async server example code that allows to reproduce the bug on the client is  
```  
//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: WebSocket SSL server, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include "server_certificate.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/strand.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void fail(beast::error_code ec, char const *what)  
{  
  std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class session : public std::enable_shared_from_this<session>  
{  
  websocket::stream<  
      beast::ssl_stream<beast::tcp_stream>>  
      ws_;  
  beast::flat_buffer buffer_;  
  
public:  
  // Take ownership of the socket  
  session(tcp::socket &&socket, ssl::context &ctx)  
      : ws_(std::move(socket), ctx)  
  {  
  }  
  
  // Start the asynchronous operation  
  void  
  run()  
  {  
    // Set the timeout.  
    beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
    // Perform the SSL handshake  
    ws_.next_layer().async_handshake(  
        ssl::stream_base::server,  
        beast::bind_front_handler(  
            &session::on_handshake,  
            shared_from_this()));  
  }  
  
  void  
  on_handshake(beast::error_code ec)  
  {  
    if (ec)  
      return fail(ec, "handshake");  
  
    // Turn off the timeout on the tcp_stream, because  
    // the websocket stream has its own timeout system.  
    beast::get_lowest_layer(ws_).expires_never();  
  
    // Set suggested timeout settings for the websocket  
    ws_.set_option(  
        websocket::stream_base::timeout::suggested(  
            beast::role_type::server));  
  
    // Set a decorator to change the Server of the handshake  
    ws_.set_option(websocket::stream_base::decorator(  
        [](websocket::response_type &res) {  
          res.set(http::field::server,  
                  std::string(BOOST_BEAST_VERSION_STRING) +  
                      " websocket-server-async-ssl");  
        }));  
  
    // Accept the websocket handshake  
    ws_.async_accept(  
        beast::bind_front_handler(  
            &session::on_accept,  
            shared_from_this()));  
  }  
  
  void  
  on_accept(beast::error_code ec)  
  {  
    if (ec)  
      return fail(ec, "accept");  
  
    // Read a message  
    do_read();  
  }  
  
  void  
  do_read()  
  {  
    // Read a message into our buffer  
    ws_.async_read(  
        buffer_,  
        beast::bind_front_handler(  
            &session::on_read,  
            shared_from_this()));  
  }  
  
  void  
  on_read(  
      beast::error_code ec,  
      std::size_t bytes_transferred)  
  {  
    boost::ignore_unused(bytes_transferred);  
  
    // This indicates that the session was closed  
    if (ec == websocket::error::closed)  
      return;  
  
    if (ec)  
      return fail(ec, "read");  
  
    // Clear the buffer  
    buffer_.consume(buffer_.size());  
  
    // Read again  
    return do_read();  
  }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
  net::io_context &ioc_;  
  ssl::context &ctx_;  
  tcp::acceptor acceptor_;  
  
public:  
  listener(  
      net::io_context &ioc,  
      ssl::context &ctx,  
      tcp::endpoint endpoint)  
      : ioc_(ioc), ctx_(ctx), acceptor_(net::make_strand(ioc))  
  {  
    beast::error_code ec;  
  
    // Open the acceptor  
    acceptor_.open(endpoint.protocol(), ec);  
    if (ec)  
    {  
      fail(ec, "open");  
      return;  
    }  
  
    // Allow address reuse  
    acceptor_.set_option(net::socket_base::reuse_address(true), ec);  
    if (ec)  
    {  
      fail(ec, "set_option");  
      return;  
    }  
  
    // Bind to the server address  
    acceptor_.bind(endpoint, ec);  
    if (ec)  
    {  
      fail(ec, "bind");  
      return;  
    }  
  
    // Start listening for connections  
    acceptor_.listen(  
        net::socket_base::max_listen_connections, ec);  
    if (ec)  
    {  
      fail(ec, "listen");  
      return;  
    }  
  }  
  
  // Start accepting incoming connections  
  void  
  run()  
  {  
    do_accept();  
  }  
  
private:  
  void  
  do_accept()  
  {  
    // The new connection gets its own strand  
    acceptor_.async_accept(  
        net::make_strand(ioc_),  
        beast::bind_front_handler(  
            &listener::on_accept,  
            shared_from_this()));  
  }  
  
  void  
  on_accept(beast::error_code ec, tcp::socket socket)  
  {  
    if (ec)  
    {  
      fail(ec, "accept");  
    }  
    else  
    {  
      // Create the session and run it  
      std::make_shared<session>(std::move(socket), ctx_)->run();  
    }  
  
    // Accept another connection  
    do_accept();  
  }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char *argv[])  
{  
  // Check command line arguments.  
  if (argc != 4)  
  {  
    std::cerr << "Usage: websocket-server-async-ssl <address> <port> <threads>\n"  
              << "Example:\n"  
              << "    websocket-server-async-ssl 0.0.0.0 8080 1\n";  
    return EXIT_FAILURE;  
  }  
  auto const address = net::ip::make_address(argv[1]);  
  auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
  auto const threads = std::max<int>(1, std::atoi(argv[3]));  
  
  // The io_context is required for all I/O  
  net::io_context ioc{threads};  
  
  // The SSL context is required, and holds certificates  
  ssl::context ctx{ssl::context::tlsv13};  
  
  // This holds the self-signed certificate used by the server  
  load_server_certificate(ctx);  
  
  // Create and launch a listening port  
  std::make_shared<listener>(ioc, ctx, tcp::endpoint{address, port})->run();  
  
  // Run the I/O service on the requested number of threads  
  std::vector<std::thread> v;  
  v.reserve(threads - 1);  
  for (auto i = threads - 1; i > 0; --i)  
    v.emplace_back(  
        [&ioc] {  
          ioc.run();  
        });  
  ioc.run();  
  
  return EXIT_SUCCESS;  
}  
```

---

## Comment 8

> Username: djarek  
> Created at: 2019-05-04 14:28:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489331854  

The issue is here: https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/ping.hpp#L179  
`this->get()` should be `get_executor()` instead, because `this->get()` returns a reference to an executor in `this` which is moved-from in `post()`.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-05-04 22:35:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489370637  

I believe that version **248-hf1** fixes it, please try it if possible. We added a unit test which reproduces the problem exactly. Thank you for your very detailed report which made this easy to track down and fix!  
https://github.com/vinniefalco/beast/tree/v248-hf1

---

## Comment 10

> Username: stenreijers  
> Created at: 2019-05-05 08:25:11 UTC  
> Updated at: 2019-05-05 08:26:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-489402948  

@vinniefalco @djarek I can confirm that with v248-hf1 the exception no longer exists. Thanks for the quick response and fix!

---

## Comment 11

> Username: mzimbres  
> Created at: 2019-10-12 22:08:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-541365411  

Same problem here with 1.70. Upgrading to 1.71 solved it.

---

## Comment 12

> Username: wtom76  
> Created at: 2025-02-20 16:21:49 UTC  
> Updated at: 2025-02-20 16:33:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-2671996873  

hi  
looks like this or same bug returned in 1.86  
1.85 is fine in this regard  
  
[no_executor.zip](https://github.com/user-attachments/files/18891485/no_executor.zip)  
  
windows, msvc

---

## Comment 13

> Username: ashtum  
> Created at: 2025-02-20 17:01:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-2672114058  

> hi looks like this or same bug returned in 1.86 1.85 is fine in this regard  
>   
> [no_executor.zip](https://github.com/user-attachments/files/18891485/no_executor.zip)  
>   
> windows, msvc  
  
Yes, it is fixed in 1.87 https://github.com/boostorg/beast/pull/2926.

---

## Comment 14

> Username: hoyhoy  
> Created at: 2025-12-12 16:40:48 UTC  
> Updated at: 2025-12-12 16:44:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-3647317938  

We just saw this in boost/1.86.0, but we inherited some code that's doing this...  
  
  
```  
auto this_connection = shared_from_this();  
beast::ssl_stream<beast::tcp_stream> ssl_stream;  
  
ssl_stream->async_write_some(  
    boost::asio::buffer((void*) buf_ptr, len),  
    boost::asio::use_future([&, this_connection](boost::beast::error_code ec, std::size_t n) {  
        return ec.value() ? 0 : n;  
    }));  
  
```  
  
@vinniefalco is it OK to invoke `use_future` inside of an asio async call?

---

## Comment 15

> Username: ashtum  
> Created at: 2025-12-12 17:09:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-3647412284  

> We just saw this in boost/1.86.0, but we inherited some code that's doing this...  
>   
> ```  
> auto this_connection = shared_from_this();  
> beast::ssl_stream<beast::tcp_stream> ssl_stream;  
>   
> ssl_stream->async_write_some(  
>     boost::asio::buffer((void*) buf_ptr, len),  
>     boost::asio::use_future([&, this_connection](boost::beast::error_code ec, std::size_t n) {  
>         return ec.value() ? 0 : n;  
>     }));  
> ```  
>   
> [@vinniefalco](https://github.com/vinniefalco) is it OK to invoke `use_future` inside of an asio async call?  
  
Using `asio::use_future` is fine. Just make sure that `fut.get()` doesn’t block the thread that executes the `io_context` when you're using a single-threaded executor.

---

## Comment 16

> Username: hoyhoy  
> Created at: 2025-12-12 17:28:10 UTC  
> Updated at: 2025-12-12 17:28:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1599#issuecomment-3647472860  

So, our `get()` is being done after the `async_write()`.  
  
```  
size_t  
connection::sync_write(  
    const void* buf,  
    size_t len) {  
  
    // used to increase ref count in the case of a timeout  
    auto this_connection = shared_from_this();   
  
    while (len > 0) {  
        std::future<std::size_t> write_result;  
        size_t sent = 0;  
  
        write_result = _ssl_stream->async_write_some(  
            boost::asio::buffer((void*) buf_ptr, len),  
            boost::asio::use_future([&, this_connection](boost::beast::error_code ec, std::size_t n) { .. })  
        );  
    }  
    sent = write_result.get();  
    len -= sent;  
}  
```
