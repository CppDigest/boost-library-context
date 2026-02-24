# #2883 - Performance issue: Beast's request per second (RPS) is half of C#'s [Closed]

> Username: albertxavier100  
> Created at: 2024-06-07 10:13:55 UTC  
> Updated at: 2024-07-24 06:19:45 UTC  
> Closed at: 2024-07-24 06:19:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2883  

### Version of Beast  
347  
  
### Steps necessary to reproduce the problem  
  
1. Install dotnet https://learn.microsoft.com/en-us/dotnet/core/install/linux  
2. Install zig: `sudo snap install zig --beta --classic`  
3. check your `zig` version and it should be 0.12 now  
4. `git clone https://github.com/zigzap/zap.git`  
6. `cd zap`  
7.  `git checkout -b perf tags/v0.7.0`  
8. In `wrk/csharp/Program.cs`, change line 6 to `app.MapGet("/", () => "Hello from C#1234");`, makes response body size (17 bytes ) the same between c# and c++ test code (little impact on result).  
9. `./wrk/measure_all.sh "csharp cpp-beast"`  
10. the perf result displays in console, you can find the cpp-beast is really poor on perf compared to csharp  
  
for more details, see https://github.com/zigzap/zap/blob/master/blazingly-fast.md#how  
  
### All relevant compiler information  
  
#### Result  
 The perf test result: https://github.com/zigzap/zap/blob/master/blazingly-fast.md#the-computer-makes-the-difference  
![result](https://raw.githubusercontent.com/zigzap/zap/master/wrk/samples/req_per_sec_graph.png)  
  
#### Beast's test code  
https://github.com/zigzap/zap/blob/master/wrk/cpp/main.cpp  
  
---  
I like beast, it's hard to believe beast's performance is so poor, can anyone point out what's wrong or improve beast's test code?  
I tried to modify to beast's http server example to return a string-body response for the perf, but the result got worse. 😭

---

## Comment 1

> Username: ashtum  
> Created at: 2024-06-11 12:16:09 UTC  
> Updated at: 2024-06-11 12:17:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2883#issuecomment-2160612870  

I'm mesuring approximately 300K requests per second using the [http_server_async](https://github.com/boostorg/beast/blob/develop/example/http/server/async/http_server_async.cpp) example and utilizing 4 threads (as stated in the provided link). However, if you create one io_context per core and use individual acceptors (using `SO_REUSEPORT` option), you can anticipate around 150K requests per second for each additional core you put into your setup (as they work independently).  
  
Regardless, I doubt this benchmark accurately reflects the usage of these libraries in real-world scenarios:  
  
> So, what exactly are these benchmarks testing?  
Simple HTTP servers that respond to GET requests with a constant, 17-byte-long message.

---

## Comment 2

> Username: sehe  
> Created at: 2024-06-12 00:31:31 UTC  
> Updated at: 2024-06-12 00:33:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2883#issuecomment-2161844358  

This is comparing apples to pears.  
  
 - The Kestrel route is probably not creating a new response each time.   
 - The C++ version is not reading any requests AND closing the connection each time. The Kestrel server keeps the connection alive e.g. when probed with `curl`. Even if you kept it alive, at some point the client might have filled the entire TCP receive buffer  and will effectively block on _sending the request_. -> You **need** to properly process requests.  
 - The C++ version used a synchronous accept loop  
   
 I compared with my own C++ version that fixes these. I have two conditional defines (`TRUE_HTTP` and `CACHED_RESPONSE`) that you can play around with. The code comments explain their (observed) effect.  
  
```c++  
#include <boost/asio.hpp>  
#include <boost/asio/experimental/awaitable_operators.hpp>  
#include <boost/beast.hpp>  
#include <boost/lexical_cast.hpp>  
#include <fstream>  
#include <iostream>  
#include <syncstream>  
  
namespace beast = boost::beast;  
namespace http  = beast::http;  
namespace net   = boost::asio;  
using tcp     = net::ip::tcp;  
using namespace net::experimental::awaitable_operators;  
using executor_t = net::thread_pool::executor_type;  
using acceptor_t = net::deferred_t::as_default_on_t<net::basic_socket_acceptor<tcp, executor_t>>;  
using socket_t   = net::deferred_t::as_default_on_t<net::basic_stream_socket<tcp, executor_t>>;  
  
[[maybe_unused]] static std::string read_html_file(std::string const& file_path) {  
    std::ifstream file(file_path, std::ios::binary);  
    return {std::istreambuf_iterator<char>(file), {}};  
}  
  
static auto const make_response_message = [](bool keep_alive) {  
    // Construct an HTTP response with the HTML content  
    std::string_view msg = "Hello from C++!!!"; // or read_html_file("hello.html");  
  
    http::response<http::span_body<char const>> res{http::status::ok, 11, msg};  
    res.set(http::field::server, "C++ Server");  
    res.set(http::field::content_type, "text/html");  
    res.keep_alive(keep_alive);  
    res.prepare_payload();  
    return res;  
};  
  
static auto const s_cooked_response = [] {  
    static auto const text = boost::lexical_cast<std::string>(make_response_message(true));  
    return net::buffer(text);  
}();  
  
net::awaitable<void, executor_t> handle_client_async(socket_t socket) try {  
    socket.set_option(tcp::no_delay(true)); // no difference observed in benchmark  
  
#ifdef TRUE_HTTP // This affects throughput by only about -10%  
    beast::flat_buffer buf;  
    for (http::request<http::empty_body> req;; req.clear()) {  
        auto [ec, _] = co_await async_read(socket, buf, req, as_tuple(net::deferred));  
        if (ec)  
            break;  
    #ifdef CACHED_RESPONSE  
        // emulate caching server  
        co_await async_write(socket, s_cooked_response);  
    #else  
        // This is a more realistic way but probably NOT what Kestrel is doing for the static route  
        // It affects throughput by about -25%  
        co_await async_write(socket, make_response_message(req.keep_alive()));  
    #endif  
        if (!req.keep_alive())  
            break;  
    }  
#else  
    // Since we're ignoring the requests, we might as well assume they're correct. (INSECURE)  
    for (beast::flat_buffer buf;;) {  
        auto [ec, n] = co_await async_read_until(socket, buf, "\r\n\r\n", as_tuple(net::deferred));  
        if (ec)  
            break;  
        buf.consume(n);  
        co_await async_write(socket, s_cooked_response);  
    }  
#endif  
} catch (beast::system_error const& e) {  
    std::osyncstream(std::cerr) << "handle_client_async error: " << e.code().message() << std::endl;  
}  
  
net::awaitable<void, executor_t> server(uint16_t port) {  
    auto ex = co_await net::this_coro::executor;  
  
    for (acceptor_t acceptor(ex, {{}, port});;)  
        co_spawn(ex,                                                    //  
                 handle_client_async(co_await acceptor.async_accept()), //  
                 net::detached);  
}  
  
int main() try {  
    // Create a thread pool  
    net::thread_pool pool(4);  
    executor_t ex = pool.get_executor();  
  
    // Create and bind the acceptor  
    co_spawn(ex, server(8070), net::detached);  
  
    std::cout << "Server listening on port 8070..." << std::endl;  
  
    pool.join();  
} catch (std::exception const& e) {  
    std::cerr << "Main error: " << e.what() << std::endl;  
}  
```  
  
On my system:  
  
 - no defines, fast route:   
![image](https://github.com/boostorg/beast/assets/324097/8ce91454-75f3-4c54-9dc1-1db03d3d0048)  
  
 - `TRUE_HTTP` and `CACHED_RESPONSE`:  
![image](https://github.com/boostorg/beast/assets/324097/56977a79-40c3-4d00-a3ea-2d9bc6dd84fa)  
   
 - `TRUE_HTTP` only:  
![image](https://github.com/boostorg/beast/assets/324097/84758c18-2f68-4a16-98ed-1d2dcd324fab)  
  
As you can see, I focused on throughput numbers (as with fixed response size it's colinear with req/s). As such, `TCP_NODELAY` has no tangible effect. You may want to play around to see what the latencies are like. You can see my work here: https://github.com/zigzap/zap/pull/110  
  
> _Enabling `BOOST_ASIO_HAS_IO_URING` and `BOOST_ASIO_DISABLE_EPOLL` did not  result in improvements._

---

## Comment 3

> Username: albertxavier100  
> Created at: 2024-06-13 16:24:09 UTC  
> Updated at: 2024-06-13 16:28:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2883#issuecomment-2166156177  

> However, if you create one io_context per core and use individual acceptors (using `SO_REUSEPORT` option)  
  
Thanks @ashtum . I'll try that and comapare to c# again.

---

## Comment 4

> Username: albertxavier100  
> Created at: 2024-06-13 16:26:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2883#issuecomment-2166160362  

Thanks @sehe , I'll try your GREAT work and compare to other languages.   
  
+ related issue: https://github.com/zigzap/zap/issues/108
