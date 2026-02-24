# #2807 - http client tcp ssl stream async_shutdown blocks forever [Closed]

> Username: RavikumarTulugu  
> Created at: 2024-01-25 11:38:00 UTC  
> Updated at: 2025-01-11 15:19:51 UTC  
> Closed at: 2024-02-17 14:30:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2807  

Our server calls into another 3rdparty https server as part of handling a http request , what we are seeing is that the client works fine and gets the response from the 3rd party server but async_shutdown blocks forever.  The client is copied from the boost beast awaitable ssl async client example.   
The client blocks in the async_shutdown despite setting the timeout for the lowest layer.  
  
As an extra step i tried setting the connection header to close so that the server would immediately close the connection. But there was no change in the behavior.   
  
I am using boost 1.82.   
Please advise on what could be possibly going wrong.   
Thanks   
  
 ```  
beast::get_lowest_layer ( stream ).expires_after ( std::chrono::seconds ( 10 ) );  
 auto [ ec ] = co_await stream.async_shutdown ( net::as_tuple ( net::use_awaitable ) );  <--- blocks forever here  
  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-25 12:11:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1910080027  

I cannot reproduce this error; in my tests, `expires_after` works fine. Please provide a minimal example that can reproduce this issue.

---

## Comment 2

> Username: RavikumarTulugu  
> Created at: 2024-01-25 12:27:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1910103714  

As described eariler,  the server is again a awaitable http server sitting behind a reverse proxy.   
just making a https request to an external  server from the handle_request routine of this example   
https://github.com/boostorg/beast/blob/develop/example/http/server/awaitable/http_server_awaitable.cpp  
should reproduce the issue. I did the same for our app.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-01-25 13:07:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1910189179  

[http_server_awaitable.cpp](https://github.com/boostorg/beast/blob/develop/example/http/server/awaitable/http_server_awaitable.cpp) does not utilize an ssl_stream connection. Are you delegating the SSL work to the reverse proxy?

---

## Comment 4

> Username: RavikumarTulugu  
> Created at: 2024-01-26 06:35:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1911555825  

nope, the reverse proxy calls into another external server when extra information is needed, There is no problem with the request response , both work fine with the external server, it is only issue with the async_shutdown, when we traced why the reverse proxy is timing out , we found out that the server is getting stuck in async_shutdown.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-01-26 07:56:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1911628482  

Could you please provide additional information? The [http_server_awaitable.cpp](https://github.com/boostorg/beast/blob/develop/example/http/server/awaitable/http_server_awaitable.cpp) is not an SSL server. How should it respond to async_shutdown?

---

## Comment 6

> Username: RavikumarTulugu  
> Created at: 2024-01-27 05:22:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1913005405  

Sorry for the confusion , here is the picture of the setup   
  [ browser ] <---------------> [ web server ] <------------------> [ beast http server ] <-------------------------> [ external https server ]   
beast http server hangs forever while closing the connection to the external https server.   
hope the picture is clear.

---

## Comment 7

> Username: ashtum  
> Created at: 2024-01-29 08:49:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1914226479  

Are you establishing an HTTPS client connection in `http_server_awaitable.cpp` as if it intends to proxy a request or something? [http_client_awaitable_ssl.cpp](https://github.com/boostorg/beast/blob/develop/example/http/client/awaitable-ssl/http_client_awaitable_ssl.cpp) should be a good example of that.  
And I couldn't reproduce this error; in my tests, expires_after works fine.

---

## Comment 8

> Username: RavikumarTulugu  
> Created at: 2024-01-29 15:34:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1914948294  

Yes , I am establishing a HTTPS connection in http_server_awaitable.cpp. I copied the client_awaitable_ssl.cpp for the client part. If you are not seeing the issue,  pls share your code, may be it is an usage issue with my code.

---

## Comment 9

> Username: ashtum  
> Created at: 2024-01-30 07:14:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1916210074  

I tested the code exactly as it is in `client_awaitable_ssl.cpp`. Have you tried to test it in isolation? maybe the issue something else.

---

## Comment 10

> Username: RavikumarTulugu  
> Created at: 2024-01-31 03:24:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1918312942  

I just added these lines in the handle_request routine of the server_awaitable.cpp , may be some thing i screwed up during copy/paste from client_awaitable_ssl.cpp , I left out some code for brevity but that should not matter.   
  
```  
template <class Body, class Allocator>  
net::awaitable<http::message_generator>  
handle_request ( ssl::context &sslctx,  
               http::request<Body,  
               http::basic_fields<Allocator>>&& req ) {  
  
auto resolver = net::use_awaitable.as_default_on ( tcp::resolver ( co_await net::this_coro::executor ) );  
    beast::ssl_stream<tcp_stream> stream {   
      net::use_awaitable.as_default_on (   
        beast::tcp_stream ( co_await net::this_coro::executor ) ),    
        sslctx };  
    std::string host = "abc.xyz.com", port = "443", target = "/token";  
    int version = 11;   
    auto const results = co_await resolver.async_resolve ( host, port );  
    beast::get_lowest_layer ( stream ).expires_after ( std::chrono::seconds ( 10 ) );  
    co_await beast::get_lowest_layer ( stream ).async_connect ( results );  
    beast::get_lowest_layer ( stream ).expires_after ( std::chrono::seconds ( 10 ) );  
    co_await stream.async_handshake ( ssl::stream_base::client );  
    http::request<http::string_body> req2 { http::verb::post, target, version };  
    req2.set ( http::field::connection, "close" );  
    req2.set ( http::field::host, host );  
    req2.set ( http::field::user_agent, BOOST_BEAST_VERSION_STRING );  
    req2.set ( http::field::content_type, "application/x-www-form-urlencoded" );  
    req2.body() = paramstring;  
    req2.set ( http::field::content_length, boost::lexical_cast<std::string> ( req2.body().size() ) );  
    beast::get_lowest_layer ( stream ).expires_after ( std::chrono::seconds ( 10 ) );  
    co_await http::async_write ( stream, req2 );  
    beast::flat_buffer b;  
    http::response<http::string_body> res2;  
    co_await http::async_read ( stream, b, res2 );  
    beast::get_lowest_layer ( stream ).expires_after ( std::chrono::seconds ( 10 ) );  
    auto [ ec ] = co_await stream.async_shutdown ( net::as_tuple ( net::use_awaitable ) ); <-------- this call never returns.   
    if ( ec == net::error::eof ) ec = {};  
    if ( ec ) throw boost::system::system_error ( ec, "shutdown" );  
    co_return res2;   
}  
```

---

## Comment 11

> Username: ashtum  
> Created at: 2024-01-31 16:30:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1919465749  

Try testing it in isolation; check whether async_shutdown in `client_awaitable_ssl.cpp` works correctly with your server or not.

---

## Comment 12

> Username: ashtum  
> Created at: 2024-02-15 08:42:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1945613014  

@RavikumarTulugu Is this resolved?

---

## Comment 13

> Username: RavikumarTulugu  
> Created at: 2024-02-17 14:30:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-1950223933  

Please close this issue for now, i didnt had time to verify this. will open, if i see the issue in future.

---

## Comment 14

> Username: imerr  
> Created at: 2024-07-09 12:57:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-2217670378  

seeing the same thing here fwiw  
async_shutdown seems to hang forever if the client isn't cooperative and `beast::get_lowest_layer ( stream ).expires_after ( std::chrono::seconds ( 10 ) );` seems to have no effect either  
Reproducable for me on windows using `openssl s_client -connect localhost:7000` as a client, have not tried a minimal example (sorry.)  
  
Ended up just doing `beast::get_lowest_layer(stream).close()` as my use case does not care about graceful shutdowns

---

## Comment 15

> Username: adjfangjiawei  
> Created at: 2025-01-11 15:19:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2807#issuecomment-2585300686  

I meet same problem.This is my code.  
   auto startTime = std::chrono::high_resolution_clock::now();  
    class NoReturnWarn {  
      public:  
        NoReturnWarn(std::chrono::system_clock::time_point& t) : _t(t) {};  
        void onTimer(Poco::Timer& timer) {  
            std::chrono::system_clock::time_point now = std::chrono::high_resolution_clock::now();  
            if (now - _t >= std::chrono::seconds(10)) {  
                spdlog::error("doHttpsRequest took more than 10 seconds. Stopping.");  
                return;  
            }  
        };  
        std::chrono::system_clock::time_point _t;  
    };  
    Poco::Timer tracePrintTimer(10000, 10000);  
    NoReturnWarn noReturnWarn(startTime);  
    Poco::TimerCallback<NoReturnWarn> tracePrintTaskcallback(noReturnWarn, &NoReturnWarn::onTimer);  
    tracePrintTimer.start(tracePrintTaskcallback);  
  
    tcp::resolver resolver(ioc);  
    auto const results = resolver.resolve(pUrl.host, pUrl.port);  
  
    beast::ssl_stream<beast::tcp_stream> stream(ioc, ctx);  
  
    // 设置SNI（Server Name Indication）  
    if (!SSL_set_tlsext_host_name(stream.native_handle(), pUrl.host.c_str())) {  
        beast::error_code ec{static_cast<int>(::ERR_get_error()), asio::error::get_ssl_category()};  
        throw beast::system_error{ec};  
    }  
  
    // 连接和握手  
    beast::get_lowest_layer(stream).expires_after(std::chrono::seconds(TIMEOUT_SECONDS));  
    beast::get_lowest_layer(stream).connect(results);  
  
    beast::get_lowest_layer(stream).expires_after(std::chrono::seconds(TIMEOUT_SECONDS));  
    try {  
        stream.handshake(ssl::stream_base::client);  
    } catch (const boost::system::system_error& e) {  
        spdlog::warn("Handshake error: {}", e.what());  
        return "";  
    }  
    SSL_set_quiet_shutdown(stream.native_handle(), 1);  
  
    // 构建并发送GET请求  
    http::request<http::string_body> req{http::verb::get, pUrl.target, 11};  
    req.set(http::field::host, pUrl.host);  
    req.set(http::field::user_agent, "Mozilla/5.0 (compatible; Boost.Beast)");  
    req.set(http::field::accept,  
            "text/html,application/xhtml+xml,application/xml;"  
            "q=0.9,*/*;q=0.8");  
    req.set(http::field::accept_language, "en-US,en;q=0.5");  
  
    beast::get_lowest_layer(stream).expires_after(std::chrono::seconds(TIMEOUT_SECONDS));  
    http::write(stream, req);  
  
    // 接收应答  
    beast::get_lowest_layer(stream).expires_after(std::chrono::seconds(TIMEOUT_SECONDS));  
    beast::flat_buffer buffer;  
    http::read(stream, buffer, res);  
  
    // 友好关闭时设置短超时，避免对端不响应导致shutdown过慢  
    beast::error_code ec;  
    setShortTimeout(stream, TIMEOUT_SECONDS);  
    stream.shutdown(ec);  
    if (ec == beast::errc::not_connected) {  
        ec.assign(0, ec.category());  
    }  
    if (ec && ec != asio::error::eof) {  
        spdlog::warn("Shutdown error: " + ec.message());  
    }  
  
    return res.body();
