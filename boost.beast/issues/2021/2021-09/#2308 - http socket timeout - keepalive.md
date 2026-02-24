# #2308 - http socket timeout / keepalive [Closed]

> Username: Moerten  
> Created at: 2021-09-03 06:44:27 UTC  
> Updated at: 2021-09-03 10:56:59 UTC  
> Closed at: 2021-09-03 08:54:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2308  

Hi, I want to establish a http socket connection for longer times. That's why I set the   
  
```  
req.keep_alive(true)  
```  
  
flag of the ...  
  
```  
http::request<http::empty_body> req;  
```  
  
...Object.  
Sadly I still experience timeouts (end of stream error 1) after about 1:30 min.  
So what is the intended way to keep the connection alive?  
Is there a ping/pong mechanism I have to use?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-09-03 07:05:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912308316  

What you are doing is correct.  
  
HTTP/1.1 requests are Keep-Alive by default provided they offer either Content-Length or are chunk-encoded.  
  
However, the keep-alive request is a merely a request. It is up to the individual server how long it wants to keep the connection alive, or even whether it wants to.  
  
The answer will entirely depend on the server you are talking to.  
  
In general, when using an existing connection the flow should be:  
  
1. set reconnect to TRUE  
2. send request  
3. receive response  
4. if no error, exit success  
5. if reconnect is FALSE, exit with error  
6. set reconnect to FALSE  
7. perform reconnect operation  
8. if error, exit with error  
9. goto 2

---

## Comment 2

> Username: Moerten  
> Created at: 2021-09-03 08:01:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912341590  

With...  
```  
1. set reconnect to TRUE  
```  
...you meant the "req.keep_alive(true)" setter function or is there a reconnect setter somewhere also?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-09-03 08:02:56 UTC  
> Updated at: 2021-09-03 08:03:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912342528  

I mean that if you are re-using a connection, there is no guarantee that it is still connected at the TCP layer. You only get to find out when you actually send or receive on the socket. So you must be prepared to re-connect and re-send the request if you get an error the first time.  
  
This is a limitation of how TCP works. The idea of a connection is a concept shared at both ends. One end may think there is a connection while the other may not.

---

## Comment 4

> Username: ddevienne  
> Created at: 2021-09-03 08:23:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912356690  

I think @madmongo1 means what's in his nice https://cppalliance.org/richard/2021/01/01/RichardsNewYearUpdate.html article, which he's too modest to repost about :)

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-09-03 08:27:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912359269  

Oh yes, I forgot about that.  
  
I just knocked this up as a demo (not tested, buyer beware)  
  
```  
#include <boost/asio.hpp>  
#include <boost/asio/experimental/as_tuple.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/core.hpp>  
  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
using error_code = beast::error_code;  
using system_error = beast::system_error;  
  
asio::awaitable< void>  
use_existing(  
    asio::ip::tcp::socket& sock,   
    asio::ip::tcp::endpoint ep,  
    beast::flat_buffer& rxbuf,  
    beast::http::request<beast::http::string_body> const& req,  
    beast::http::response<beast::http::string_body>& resp)  
{  
    auto token = asio::experimental::as_tuple(asio::use_awaitable);  
    auto reconnect = true;  
    error_code ec;  
    std::size_t size;  
  
again:  
  
    resp = {};  
  
    std::tie(ec, size) = co_await   
        beast::http::async_write(sock, req, token);  
  
    if (!ec)  
        std::tie(ec, size) = co_await   
            beast::http::async_read(sock, rxbuf, resp, token);  
  
    if(!ec)  
        co_return;  
  
    if (reconnect)  
    {  
        reconnect = false;  
        sock.close();  
        std::tie(ec) = co_await   
            sock.async_connect(ep, token);  
        if (!ec)  
            goto again;  
    }  
  
  
    throw system_error(ec);  
}  
```

---

## Comment 6

> Username: ddevienne  
> Created at: 2021-09-03 08:39:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912367093  

What if you're still stuck on C++17 or less Richard?

---

## Comment 7

> Username: Moerten  
> Created at: 2021-09-03 08:40:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912367584  

Ah ok, so the way to handle this is to simply reconnect. At the end that's what I am doing already but without coroutines. But the problem with this approach is that our socket classes code will log every disconnect. So I am getting a logfile full of connect/disconnect every 1:30 min. Is there no way keeping the connection really open. I am not a HTTP socket expert. But isn't there a simple get request possible every server will response to? Then I could use this get request as a ping to keep it open. In my case this would be enough.

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-09-03 08:44:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912370019  

It will entirely depend on the HTTP server you're speaking to. If you control it, there's no reason you can't implement your own `/ping` route, or just increase the timeout.  
  
If you're not in control of it then you're out of luck. I suspect you've put too much "intelligence" in your connection class. HTTP is very closely bound with the underlying stream. As the above shows, detecting a disconnected socket is not necessarily a logworthy event on the client. Disconnections are _normal and expected_.

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-09-03 08:45:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912370805  

> What if you're still stuck on C++17 or less Richard?  
  
Wherever you see a co_await you can mentally clip the code into a new completion handler function. That's all a coroutine is - a bunch of completion handlers stitched together.

---

## Comment 10

> Username: Moerten  
> Created at: 2021-09-03 08:54:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912375970  

Ok, then I have to accept this behaviour :).

---

## Comment 11

> Username: ddevienne  
> Created at: 2021-09-03 10:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912429121  

> you can mentally clip the code into a new completion handler   
  
Sorry, but this seems a bit abstract to me...  
  
> In this implementation I will be providing a C++20 coroutine interface over Asio executors once again.  
> I am using coroutines because they are easier to write when compared to Asio’s composed operations,  
> but are fundamentally the same thing in a prettier package  
  
I know you're basically restating the above (from your article), but again I'm having trouble make that leap for myself :(

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-09-03 10:22:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912431439  

Would you like me to rewrite it in terms of completion handler functions?

---

## Comment 13

> Username: madmongo1  
> Created at: 2021-09-03 10:37:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912440413  

Something like this:  
  
```  
template<class CompletionHandler>  
auto async_use_existing(  
    asio::ip::tcp::socket& sock,   
    asio::ip::tcp::endpoint ep,  
    beast::flat_buffer& rxbuf,  
    beast::http::request<beast::http::string_body> const& req,  
    beast::http::response<beast::http::string_body>& resp,   
    CompletionHandler&& handler)  
{  
    return asio::async_compose<CompletionHandler, void(error_code)>(  
        [&sock, ep, &rxbuf, &req, &resp, reconnect = true, coro = asio::coroutine()]  
        (auto&& self, error_code ec = {}, std::size_t = 0)   
        mutable  
        {  
            BOOST_ASIO_CORO_REENTER(coro)  
            for(;;)  
            {  
                BOOST_ASIO_CORO_YIELD  
                    beast::http::async_write(sock, req, std::move(self));  
                if (!ec)  
                    BOOST_ASIO_CORO_YIELD  
                        beast::http::async_read(sock, rxbuf, resp, std::move(self));  
                if (!ec) {  
                    self.complete(ec);  
                    return;  
                }  
  
                if (reconnect) {  
                    reconnect = false;  
                    sock.close();  
                    rxbuf.clear();  
                    BOOST_ASIO_CORO_YIELD  
                        sock.async_connect(ep, std::move(self));  
                    if(!ec) continue;  
                }  
  
                self.complete(ec);  
                return;  
            }  
  
        }, handler, sock);  
}  
  
  
void test(asio::ip::tcp::socket& sock,   
    asio::ip::tcp::endpoint ep,  
    beast::flat_buffer& rxbuf,  
    beast::http::request<beast::http::string_body> const& req)  
{  
    auto resp = std::make_shared<beast::http::response<beast::http::string_body>>();  
    async_use_existing(sock, ep, rxbuf, req, *resp, [resp](error_code){  
        // handle response;  
    });  
}  
```

---

## Comment 14

> Username: madmongo1  
> Created at: 2021-09-03 10:55:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912449585  

Expressed as a "traditional" chain of continuations:  
  
```  
struct active_request  
{  
    asio::ip::tcp::socket& sock;  
    asio::ip::tcp::endpoint ep;  
    beast::flat_buffer rxbuf;  
    beast::http::request<beast::http::string_body> req;  
    beast::http::response<beast::http::string_body> resp;  
  
    asio::any_io_executor exec_ { sock.get_executor() };  
    bool reconnect = true;  
  
    virtual void on_done(error_code ec);  
  
    void start()  
    {  
        asio::dispatch(asio::bind_executor(exec_, std::bind(&active_request::step1, this)));  
    }  
  
    void step1()  
    {  
        beast::http::async_write(sock,   
            req,   
            asio::bind_executor(exec_,   
                [this](auto...args){  
                    this->handle_write(args...);  
                }));  
    }  
  
    void handle_write(error_code ec, std::size_t size)  
    {  
        if (!ec)  
            beast::http::async_read(  
                sock,   
                rxbuf,   
                resp,   
                asio::bind_executor(  
                    exec_,   
                    [this](auto...args) {  
                        this->handle_read(args...);  
                    }));  
        else  
            handle_read(ec, size);  
  
    }  
  
    void   
    handle_read(error_code ec, std::size_t)  
    {  
        if (!ec)  
            on_done(ec);  
        else if(reconnect)  
        {  
            reconnect = false;  
            sock.close();  
            rxbuf.clear();  
            sock.async_connect(  
                ep,  
                asio::bind_executor(  
                    exec_,   
                    [this](auto...args) {  
                        this->handle_connect(args...);  
                    }));  
        }  
        else  
            handle_connect(ec);  
    }  
  
    void  
    handle_connect(error_code ec)  
    {  
        if (ec)  
            this->on_done(ec);  
        else  
            step1();  
    }  
};  
```

---

## Comment 15

> Username: madmongo1  
> Created at: 2021-09-03 10:56:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2308#issuecomment-912450160  

(no doubt you can see why I prefer coroutines!)
