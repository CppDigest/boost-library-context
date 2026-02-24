# #1591 - bad chunk error [Closed]

> Username: JosefK33  
> Created at: 2019-04-23 17:09:39 UTC  
> Updated at: 2019-06-04 09:24:05 UTC  
> Closed at: 2019-05-30 13:37:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1591  

### Version of Beast  
  
1.0.0-v189  
  
### Simple program code to reproduce  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/error.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
int main()  
{  
    try {  
        auto const host = "www.wslx.dealerconnection.com";  
        auto const port = "443";  
        auto const target = "/login.cgi";  
        int version = 11;  
  
        boost::asio::io_context ioc;  
        ssl::context ctx{ ssl::context::tlsv12 };  
        ctx.set_verify_mode(ssl::verify_none);  
  
        tcp::resolver resolver{ ioc };  
        ssl::stream<tcp::socket> stream{ ioc, ctx };  
  
        if (!SSL_set_tlsext_host_name(stream.native_handle(), host)) {  
            boost::system::error_code ec{ static_cast<int>(::ERR_get_error()), boost::asio::error::get_ssl_category() };  
            throw boost::system::system_error{ ec };  
        }  
  
        auto const results = resolver.resolve(host, port);  
        boost::asio::connect(stream.next_layer(), results.begin(), results.end());  
        stream.handshake(ssl::stream_base::client);  
  
        http::request<http::string_body> req{ http::verb::get, target, version };  
        req.set(http::field::host, host);  
        req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
        http::write(stream, req);  
  
        boost::beast::flat_buffer buffer;  
        http::response<http::dynamic_body> res;  
        http::read(stream, buffer, res);  
  
        boost::system::error_code ec;  
        stream.shutdown(ec);  
        if (ec == boost::asio::error::eof) {  
            ec.assign(0, ec.category());  
        }  
        if (ec)  
            throw boost::system::system_error{ ec };  
    }  
    catch (std::exception const& e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```  
  
### Compiler information  
  
vs 2017 (15.9.10) or  
Xcode v. 10.1  
  
### Other  
I assume it might be a server side issue, it seems server doubles response, but it is loaded OK by browsers (Chrome, Firefox).  
Although curl v7.64 has the same troubles with chunked-encoding while curl v7.54 loads the page perfectly fine as well.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-23 17:45:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-485904953  

It would be helpful to see the exact serialized HTTP response that the server is giving back.

---

## Comment 2

> Username: JosefK33  
> Created at: 2019-04-24 13:19:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-486225895  

Here is buffer dumped  
[resp_chunk.txt](https://github.com/boostorg/beast/files/3112414/resp_chunk.txt)

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-05-24 14:11:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-495646586  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-05-26 16:48:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-496014869  

Looks like the response is incomplete.

---

## Comment 5

> Username: JosefK33  
> Created at: 2019-05-28 11:51:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-496484146  

You mean file that I've attached contains incomplte response or that server sends incomplete response?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-05-28 14:34:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-496543332  

The attached file is not a complete response

---

## Comment 7

> Username: JosefK33  
> Created at: 2019-05-29 17:31:12 UTC  
> Updated at: 2019-05-29 17:31:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-497034105  

Yes, but that's all I get on read before bad chunk error.  
Nevertheless, I have a suspicion that it's an openssl v 1.1.1 issue, 'cause I checked curl 7.65 built with openssl v1.1.1.c and the same 7.65 built with WinSSL and the first one shows the same chunk error.

---

## Comment 8

> Username: JosefK33  
> Created at: 2019-05-30 13:37:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-497331844  

Just tried to use openssl 1.0.2 instead of 1.1.1and it worked perfectly fine, thus apparently it's an openssl v1.1.1 issue. Sorry for this.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-05-30 14:28:47 UTC  
> Updated at: 2019-05-30 14:29:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-497349776  

It might not be OpenSSL, it could be Boost.Asio. Try Boost 1.70 and the latest OpenSSL? Asio recently fixed a bug in the SSL stream.

---

## Comment 10

> Username: JosefK33  
> Created at: 2019-05-30 14:35:38 UTC  
> Updated at: 2019-06-04 09:24:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-497352231  

I thought about it, but since curl doesn't use boost I decided that it has nothing to do with boost. Anyway thanks for a hint, I'll try.

---

## Comment 11

> Username: JosefK33  
> Created at: 2019-05-31 13:55:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1591#issuecomment-497717632  

No luck with boost 1.70 so far. Digging into openssl 1.1.1.
