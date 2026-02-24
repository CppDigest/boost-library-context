# #2709 - "Stream truncated" error when reading http stream chunks. [Closed]

> Username: yandexgas  
> Created at: 2023-07-28 12:12:23 UTC  
> Updated at: 2024-01-23 07:15:51 UTC  
> Closed at: 2024-01-23 07:15:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2709  

Using beast version 330. Using GCC compiler version 11.1.0.  
  
  
I use `boost::beast` to get data over HTTPS stream. (in particular from [EXANTE API](https://api-live.exante.eu/api-docs/#tag/Orders-stream-API "orders HTTP stream ") ).  
My code is almost identical to the example from the official documentation of the Beast library: [Chunked encoding](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_http/chunked_encoding.html "Parsing Chunks").  
  
Everything works correctly, but periodically reading chunks is interrupted by `"Stream truncated"` or `"Partial message"` errors. I can't understand the cause of these errors. Is it in my code, or is it a problem with the internet connection or the server, or maybe the connection is interrupted by a proxy server or firewall (idk).  
  
A maximally simplified version of my code is shown below.  
P.S. The data is from a test account, so it is not secret.  
  
    #include <string>  
    #include <iostream>  
      
    #include <boost/archive/iterators/base64_from_binary.hpp>  
    #include <boost/archive/iterators/transform_width.hpp>  
    #include <boost/algorithm/string.hpp>  
      
    #include <boost/asio/connect.hpp>  
    #include <boost/asio/ip/tcp.hpp>  
    #include <boost/asio/ssl/stream.hpp>  
      
    #include <boost/beast.hpp>  
    #include <boost/beast/core.hpp>  
    #include <boost/beast/ssl.hpp>  
    #include <boost/beast/http.hpp>  
      
    using namespace std::string_literals;  
    namespace net   = boost::asio;  
    namespace ssl   = boost::asio::ssl;  
    namespace ip    = boost::asio::ip;  
    namespace beast = boost::beast;  
    namespace http  = boost::beast::http;  
    using tcp = boost::asio::ip::tcp;  
      
    std::string base64_encode(const std::string& val) {  
        using namespace boost::archive::iterators;  
        using It = base64_from_binary<transform_width<std::string::const_iterator, 6, 8>>;  
        auto tmp = std::string(It(std::begin(val)), It(std::end(val)));  
        return tmp.append((3 - val.size() % 3) % 3, '=');  
    }  
      
    int main() {  
      
        const auto clientID = "6dc42e50-0b01-444e-bae0-3a431c88b525"s;  
        const auto key = "TTQmWnbHlr5OWlNSWl1P"s;  
        const auto host = "api-demo.exante.eu"s;  
        const auto target = "/trade/3.0/stream/orders"s;  
        const auto port = "443"s;  
        const auto http_version = 11;  
        const auto auth_token = "Basic "s + base64_encode(clientID + ':' + key);  
        const auto accept_value = "application/x-json-stream"s;  
        const auto timeout = std::chrono::seconds(12);  
      
        http::request<http::string_body> request{http::verb::get, target, http_version};  
        request.set(http::field::host, host);  
        request.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
        request.set(http::field::authorization, auth_token);  
        request.set(http::field::accept, accept_value);  
        request.prepare_payload();  
      
        net::io_context ioc;  
      
        ssl::context ctx(ssl::context::tlsv12_client);  
        tcp::resolver resolver(ioc);  
        beast::ssl_stream<beast::tcp_stream> stream(ioc, ctx);  
        if (!SSL_set_tlsext_host_name(stream.native_handle(), host.c_str())) {  
            beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
            throw beast::system_error{ec};  
        }  
        auto const results = resolver.resolve(host, port);  
      
        beast::get_lowest_layer(stream).connect(results);  
        stream.handshake(ssl::stream_base::client);  
      
        http::write(stream, request);  
      
        beast::flat_buffer buffer;  
        http::parser<false, http::empty_body> p;  
      
        http::read_header(stream, buffer, p);  
      
        http::chunk_extensions ce;  
        std::string chunk;  
      
        auto header_cb =  
                [&](std::uint64_t size,  
                    beast::string_view extensions,  
                    beast::error_code &ev) {  
                    ce.parse(extensions, ev);  
                    if (ev)  
                        return;  
      
                    if (size > (std::numeric_limits<std::size_t>::max)()) {  
                        ev = http::error::body_limit;  
                        return;  
                    }  
      
                    chunk.reserve(static_cast<std::size_t>(size));  
                    chunk.clear();  
                };  
        p.on_chunk_header(header_cb);  
      
        auto body_cb =  
                [&](std::uint64_t remain,  
                    beast::string_view body,  
                    beast::error_code &ec) {  
                    if (remain == body.size())  
                        ec = http::error::end_of_chunk;  
      
                    chunk.append(body.data(), body.size());  
      
                    return body.size();  
                };  
        p.on_chunk_body(body_cb);  
      
        std::function<void(const beast::error_code &, size_t)> onRead = [&](const auto &ec, auto bytes_transferred) {  
            if (ec && ec != http::error::end_of_chunk)  
                throw std::runtime_error(ec.what());  
      
            for (auto const &extension: ce) {  
                std::cout << "Extension: " << extension.first;  
                if (!extension.second.empty())  
                    std::cout << " = " << extension.second << std::endl;  
                else  
                    std::cout << std::endl;  
            }  
      
            static int counter = 0;  
            std::cout << "Chunk Body: " << chunk << counter++ << " @@@!" << std::endl;  
            chunk.clear();  
            if (!p.is_done()) {  
                stream.next_layer().expires_after(timeout);  
                http::async_read(stream, buffer, p, onRead);  
            }  
        };  
      
        stream.next_layer().expires_after(timeout);  
        http::async_read(stream, buffer, p, onRead);  
      
        ioc.run();  
          
        return 0;  
    }  
  
I have tried both synchronous and asynchronous versions of `http::read` functions, the problem occurs in any case.  
  
The error occurs on three different devices on different networks.  
  
According to the API developers, there are no restrictions on the duration of HTTP stream listening.  
The problem occurs every time after a random period of time from a couple of minutes to several hours.

---

## Comment 1

> Username: markmaker  
> Created at: 2024-01-13 13:08:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1890453628  

I see the same error messages.  
  
But I noticed that some examples suppress this error, and in comments give a reason:  
  
https://github.com/boostorg/beast/blob/9d05ef58d19672d91475e6be6f746d9bdb031362/example/http/server/async-ssl/http_server_async_ssl.cpp#L209-L234  
  
Other flavors of the examples don't:  
  
https://github.com/boostorg/beast/blob/9d05ef58d19672d91475e6be6f746d9bdb031362/example/http/server/sync-ssl/http_server_sync_ssl.cpp#L206-L211  
  
Perhaps an omission in that second example?  
  
In any case, I assume we can ignore this error?   
  
I would appreciate if somebody more knowledgeable could confirm. Thank you.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-01-15 08:43:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1891579608  

@markmaker The same error can also occur in [http_server_sync_ssl.cpp](https://github.com/boostorg/beast/blob/9d05ef58d19672d91475e6be6f746d9bdb031362/example/http/server/sync-ssl/http_server_sync_ssl.cpp#L206-L211); it is just not ignored in that example. This error can arise from both non-conforming clients and servers.  
You can find a similar discussion in https://github.com/boostorg/beast/issues/38 and https://github.com/boostorg/beast/issues/824.

---

## Comment 3

> Username: markmaker  
> Created at: 2024-01-15 09:15:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1891642743  

I get these using [ab](https://httpd.apache.org/docs/2.4/programs/ab.html) against [http_server_sync_ssl.cpp](https://github.com/boostorg/beast/blob/9d05ef58d19672d91475e6be6f746d9bdb031362/example/http/server/sync-ssl/http_server_sync_ssl.cpp#L206-L211). Maybe `ab` is the culprit?

---

## Comment 4

> Username: ashtum  
> Created at: 2024-01-15 09:31:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1891679710  

Try using curl to see if any errors occur; it is known to be a good citizen.

---

## Comment 5

> Username: markmaker  
> Created at: 2024-01-15 14:42:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1892304057  

I tried `curl` but is is so slow and cumbersome, no error, but I doubt the result is significant.  
  
Note, `ab` only generates the error from time to time, with massive barrage.

---

## Comment 6

> Username: ashtum  
> Created at: 2024-01-15 14:55:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1892326659  

I didn't mean using curl for load test, just to make sure there is no error.  
  
> Note, ab only generates the error from time to time, with massive barrage.  
  
This might be a bug in `ab` then.

---

## Comment 7

> Username: ashtum  
> Created at: 2024-01-18 11:23:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1898293425  

@yandexgas Is this still open?

---

## Comment 8

> Username: markmaker  
> Created at: 2024-01-18 12:29:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1898389888  

I wasn't able to determine who's fault it is. So let's close it for now.

---

## Comment 9

> Username: markmaker  
> Created at: 2024-01-18 12:30:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2709#issuecomment-1898391788  

Oops, sorry, you asked @yandexgas
