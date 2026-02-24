# #2345 - tcp_stream causing SIGABRT | Boost 1.73.0 [Open]

> Username: mhassanshafiq  
> Created at: 2021-11-19 12:46:58 UTC  
> Updated at: 2023-07-14 07:29:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2345  

I am hitting a crash in my websocket server due to sigabrt caused by   
`boost/beast/core/detail/stream_base.hpp:81`  
  
All the read/write/close operations are being done in the same strand on the io-context for this web socket server.  
Upon, Vinnie's suggestion I replaced tcp_stream with tcp socket, and the crash went away.   
Tries with tcp_stream and only one single thread in io-context as well, and the crash happened again, implicating the issue is probably in tcp_stream.  
  
  
I am attaching the stack trace file here, which has some additional info added in it. The crash occurrence has a timestamp as well, and if we look at step # 31, I have pasted timestamp at this point as well. The thread which crashed seems to be stuck somewhere for more than 3 minutes trying to do a write operation.  
  
The crash is a bit rare but is reproducible under load. Even without significant load the program crashes sometimes with same stacktrace.  
  
I cannot provide the whole transport layer here as the code is proprietary, but if needed we can figure out a way to work around that.   
  
The program is running on  
 `CentOS Linux release 7.9.2009 (Core)` compiled with `gcc (GCC) 8.3.1 20190311 (Red Hat 8.3.1-3)`.  
  
[stack trace.txt](https://github.com/boostorg/beast/files/7570287/stack.trace.txt)

---

## Comment 1

> Username: mhassanshafiq  
> Created at: 2021-11-19 12:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-974042916  

@vinniefalco as discussed on the email, opening a bug here, let me know if we need any more information.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1008220689  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-16 14:29:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1157727131  

Does this still happen?

---

## Comment 4

> Username: rickhan  
> Created at: 2022-08-10 09:37:00 UTC  
> Updated at: 2022-08-10 09:41:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1210418959  

I'm using boost 1.74, on debian 11 compile with ```gcc 10.2.1 20210110 (Debian 10.2.1-6)```  
crash on close ssl connection  
```  
beast::ssl_stream<beast::tcp_stream>.async_shutdown  
```  
  
stacktrace:  
```  
#0  __GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:50  
#1  0x00007f8adbf39537 in __GI_abort () at abort.c:79  
#2  0x00007f8adbf3940f in __assert_fail_base (fmt=0x7f8adc0a2128 "%s%s%s:%u: %s%sAssertion `%s' failed.\n%n",   
    assertion=0x55ab6ff5624e "! b_", file=0x55ab6ff61c48 "/usr/include/boost/beast/core/detail/stream_base.hpp", line=80,   
    function=<optimized out>) at assert.c:92  
#3  0x00007f8adbf48662 in __GI___assert_fail (assertion=assertion@entry=0x55ab6ff5624e "! b_",   
    file=file@entry=0x55ab6ff61c48 "/usr/include/boost/beast/core/detail/stream_base.hpp", line=line@entry=80,   
    function=function@entry=0x55ab6ff5db68 "boost::beast::detail::stream_base::pending_guard::pending_guard(bool&)")  
    at assert.c:101  
#4  0x000055ab6fc753e3 in boost::beast::detail::stream_base::pending_guard::pending_guard (b=<optimized out>,   
    this=<optimized out>) at /usr/include/boost/beast/core/detail/stream_base.hpp:80  
```

---

## Comment 5

> Username: madmongo1  
> Created at: 2022-08-10 09:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1210427716  

You are either:  
- initiating two or more async_shutdowns at once, or  
- you are not preserving the lifetime of the websocket stream, or  
- some other race condition

---

## Comment 6

> Username: rickhan  
> Created at: 2022-08-10 11:01:01 UTC  
> Updated at: 2022-08-10 11:02:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1210514905  

> You are either:  
>   
> * initiating two or more async_shutdowns at once, or  
> * you are not preserving the lifetime of the websocket stream, or  
> * some other race condition  
  
This is my shutdown code,  it run only in main thread  
```  
    if (closing_) return;  
    closing_ = true;  
    auto self(shared_from_this());  
    //beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(10));  // would this be necessary?  
    stream_.async_shutdown(beast::bind_front_handler([self] (beast::error_code ec) {}));  
```

---

## Comment 7

> Username: madmongo1  
> Created at: 2022-08-10 11:14:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1210527853  

Difficult to tell from here. Any chance you can roll a minimal 1-page program that exhibits the problem?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-08-10 13:42:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1210693454  

As a workaround use a regular Asio TCP stream and manually operate your own timer. This is demonstrated in some of the Asio examples.

---

## Comment 9

> Username: rickhan  
> Created at: 2022-08-11 03:16:08 UTC  
> Updated at: 2022-08-11 03:29:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1211511598  

> Difficult to tell from here. Any chance you can roll a minimal 1-page program that exhibits the problem?  
  
sorry for waiting & thanks for your reply!  
  
example.cpp  
```  
#include <iostream>  
#include <string>  
#include <unordered_map>  
#include <functional>  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
  
  
namespace beast         = boost::beast;  
namespace http          = beast::http;  
namespace ssl           = boost::asio::ssl;  
using context           = boost::asio::ssl::context;  
using io_service        = boost::asio::io_service;  
using resolver          = boost::asio::ip::tcp::resolver;  
using string            = std::string;  
using map               = std::unordered_map<string, string>;  
using callback          = std::function<void (bool, const string&)>;  
using error_code        = boost::system::error_code;  
using endpoint          = boost::asio::ip::tcp::endpoint;  
using steady_timer      = boost::asio::steady_timer;  
  
class AsyncHttpsClient : public std::enable_shared_from_this<AsyncHttpsClient>   
{  
public:  
    AsyncHttpsClient(io_service& ios)   
        : resolver_(ios), context_(context::tlsv12_client), stream_(ios, context_)   
        {  
            context_.set_default_verify_paths();  
            req_.version(11);  
            req_.set(http::field::connection, "close");  
        }  
  
      
    void set_callback(const callback& cb) { callback_ = cb; }  
      
    void post(const string& host, const string& port, const string& target, const map& headers, const string& body)  
    {  
        if (!SSL_set_tlsext_host_name(stream_.native_handle(), host.c_str()))   
        {  
            beast::error_code ec{ static_cast<int>(::ERR_get_error()), boost::asio::error::get_ssl_category() };  
            handle_callback(false, ec.message());  
            return;  
        }  
          
        req_.method(http::verb::post);  
        req_.target(target);  
        req_.set(http::field::content_type, "application/json");  
        req_.set(http::field::content_length, std::to_string(body.length()));  
        req_.body() = body;  
        req_.prepare_payload();  
        host_ = host;  
        port_ = port;  
        start();  
    }  
      
private:  
    void start()  
    {  
        auto self(shared_from_this());  
        resolver_.async_resolve(host_, port_, beast::bind_front_handler(  
        [self, this] (const error_code& err, const resolver::results_type& eps)   
        {  
            if (closed_) return;  
            if (err)   
            {  
                handle_callback(false, err.message());  
                return;  
            }   
  
            beast::get_lowest_layer(stream_).async_connect(eps, [self, this](const error_code& ec, const endpoint& ep)   
            {  
                if (ec)   
                {  
                    handle_callback(false, ec.message());  
                    return;  
                }  
  
                stream_.async_handshake(ssl::stream_base::client, [self, this](beast::error_code ec)   
                {  
                    if (ec)   
                    {  
                        handle_callback(false, ec.message());  
                        return;  
                    }  
  
                    start_write();  
                });  
            });  
        }));  
    }  
      
    void start_write()   
    {  
        if (closed_) return;  
        auto self(shared_from_this());  
        http::async_write(stream_, req_, beast::bind_front_handler([self, this](beast::error_code ec, size_t len)   
        {  
            if (ec)   
            {  
                handle_callback(false, ec.message());  
                return;  
            }  
  
            do_read();  
        }));  
    }  
      
    void do_read()   
    {  
        if (closed_) return;  
        auto self(shared_from_this());  
        http::async_read(stream_, buffer_, res_, beast::bind_front_handler([self, this](beast::error_code ec, size_t len) {  
            if (ec)   
            {  
                handle_callback(false, ec.message());  
                return;  
            }  
  
            if (res_.result() != beast::http::status::ok)   
            {  
  
                auto result_view = obsolete_reason(res_.result());  
                std::string result(result_view.data(), result_view.size());  
                handle_callback(false, result);  
                do_shutdown();  
                return;  
            }  
            handle_callback(true, res_.body());  
            do_shutdown();  
        }));  
    }  
      
    void do_shutdown()   
    {  
        if (closed_) return;  
        closed_ = true;  
        auto self(shared_from_this());  
        beast::error_code ec;  
        stream_.async_shutdown(beast::bind_front_handler([self] (beast::error_code ec) {}));  
    }  
      
    void handle_callback(bool result, const string& reason_or_reply)   
    {  
        if (callback_) callback_(result, reason_or_reply);  
    }  
      
private:  
    resolver resolver_;  
    context context_;  
    beast::ssl_stream<beast::tcp_stream> stream_;  
    beast::flat_buffer buffer_;  
    http::request<http::string_body> req_;  
    http::response<http::string_body> res_;  
      
    bool closed_ = false;  
    callback callback_;  
    string host_;  
    string port_;  
};  
  
io_service ios;  
steady_timer timer(ios);  
  
void do_task()  
{  
    timer.expires_after(std::chrono::seconds(1));  
    timer.async_wait([] (const error_code& e)   
    {  
        if (e)  
        {  
            std::cout << e.message() << std::endl;  
            return;  
        }  
          
        auto client = std::make_shared<AsyncHttpsClient>(ios);  
        client->set_callback([] (bool succeed, const string& reason_or_reply)   
        {  
            std::cout << "post result:" << succeed << " reason_or_reply= "   
                      << reason_or_reply << std::endl;  
        });  
          
        map header = {{"Content-Type", "application/json"}};  
        client->post("httpbin.org", "443", "/post", header, "{\"ids\":[123456]}");  
        do_task();  
    });  
}  
  
int main(int argc, char** argv)   
{  
    do_task();  
    ios.run();  
    return 0;  
}  
```  
compile command: ``` g++ -std=c++17 example.cpp -lboost_system -lpthread -ldl -lssl -lcrypto ```  
it works correct for many days and then suddenly it just came to assert failed.  
did I use beast wrong way? thanks for your reply

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-08-11 03:19:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1211513056  

It looks like you are creating temporary `std::string` variables when you call `post`, then passing those temporaries to `async_resolve` (which accepts them as `string_view`). But when `post` returns, those strings are no longer valid. You need to store those as `std::string` data members in your class until the `async_resolve` operation completes.

---

## Comment 11

> Username: rickhan  
> Created at: 2022-08-11 03:30:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1211517282  

> It looks like you are creating temporary `std::string` variables when you call `post`, then passing those temporaries to `async_resolve` (which accepts them as `string_view`). But when `post` returns, those strings are no longer valid. You need to store those as `std::string` data members in your class until the `async_resolve` operation completes.  
  
I update my example code, I did store host and port as data members in reality

---

## Comment 12

> Username: madmongo1  
> Created at: 2022-08-11 08:35:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1211698753  

What is the text in the assertion message?  
What is actually failing?

---

## Comment 13

> Username: madmongo1  
> Created at: 2022-08-11 08:40:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1211702891  

I have it compiled on my home machine. I'll leave it running in a debugger while I head to the office.  
  
I'm currently seeing this output:  
```  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
post result:0 reason_or_reply= Bad Request  
...  
```  
  
Is that correct?

---

## Comment 14

> Username: rickhan  
> Created at: 2022-08-11 08:44:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1211707148  

> I have it compiled on my home machine. I'll leave it running in a debugger while I head to the office.  
>   
> I'm currently seeing this output:  
>   
> ```  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> post result:0 reason_or_reply= Bad Request  
> ...  
> ```  
>   
> Is that correct?  
  
yes, and I found if I remove  the line ``` stream_.async_shutdown(beast::bind_front_handler([self] (beast::error_code ec) {})); ``` in my project, everything seems normal now.

---

## Comment 15

> Username: madmongo1  
> Created at: 2022-08-11 09:47:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1211765961  

```  
(beast::bind_front_handler([self] (beast::error_code ec) {})  
```  
this looks a bit suspect. The `bind_front_handler` isn't necessary here as you're capturing `self` in the function object itself.

---

## Comment 16

> Username: rickhan  
> Created at: 2022-08-11 09:49:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1211767725  

> ```  
> (beast::bind_front_handler([self] (beast::error_code ec) {})  
> ```  
>   
> this looks a bit suspect. The `bind_front_handler` isn't necessary here as you're capturing `self` in the function object itself.  
  
ok, I will try remove bind_fron_handler, thx~

---

## Comment 17

> Username: gopalak  
> Created at: 2022-08-16 23:22:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1217267436  

@vinniefalco @madmongo1 This is exactly what I see as well. See my earlier post on assert. I have now just 1 thread for the io_ctx as well. This also happens only under load I am not able to recreate this in a lighter load. I also have asserts to make sure async calls are not called till the earlier one returns in my code and none of them trigger. But I also see the same SIGABRT under load (only difference mine is a SSL websocket stream) Boost 1.75 and centos 7.9 as well

---

## Comment 18

> Username: madmongo1  
> Created at: 2022-08-17 11:18:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1217875436  

Are we able to build a compilable single file stress test to try to reproduce?

---

## Comment 19

> Username: gopalak  
> Created at: 2022-08-17 11:27:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1217884816  

Hi  
  
At least from my side the load generation which is causing this is seen in  
prod and I’m trying to see if I can generate that kind of load.  
  
Will try next week again but this comes all  
Of a sudden and clearly I can assure there is no pending asynchronous IO  
calls as I said there is 1 thread for the IO context and I have assert  
state te tracking separate booleans to guard read, write and close async  
calls in the code.  
  
None of them are triggered at any of these ABORT which happened in  
production  
  
Cheers  
Gops  
  
On Wed, Aug 17, 2022 at 07:18 Richard Hodges ***@***.***>  
wrote:  
  
> Are we able to build a compilable single file stress test to try to  
> reproduce?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2345#issuecomment-1217875436>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ACL6DOSQ4DJPWBNEP5DGX2DVZTC7NANCNFSM5IMB7TJA>  
> .  
> You are receiving this because you commented.Message ID:  
> ***@***.***>  
>  
--   
Computers are like air conditioners - they stop working properly if you  
open Windows

---

## Comment 20

> Username: gopalak  
> Created at: 2022-08-17 20:18:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1218451880  

Just to give some context, the last 20+ hrs haven't seen this happen. Like the OP mentioned this comes al of a sudden which is what is making it very tough to reproduce.

---

## Comment 21

> Username: gopalak  
> Created at: 2022-08-17 21:09:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1218492635  

@mhassanshafiq can  u elaborate when u say u replaced tcp_stream with tcp socket and the crash went away...

---

## Comment 22

> Username: gopalak  
> Created at: 2022-08-17 21:37:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1218513612  

@vinniefalco can u please share what the soluton u suggested for this where looks like the crash  was not happening. I use a ssl_stream for TLS 1.2 connection. I just want to be sure of what was suggested so I can  try the same if that resolves this issue. thanks

---

## Comment 23

> Username: vinniefalco  
> Created at: 2022-08-17 22:16:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1218546385  

just switch to an ssl stream with  a regular ASIO socket, and implement the timeout yourself.

---

## Comment 24

> Username: mhassanshafiq  
> Created at: 2022-08-18 07:42:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1219143677  

```  
Try replacing beast::tcp_stream with boost::asio::ip::tcp::socket  
instead, and comment out the timeouts. This will help you isolate the  
problem. Maybe it is a bug in Beast? I doubt it, but you never know.  
```  
  
This is the suggestion I followed to work around the crash. @gopalak

---

## Comment 25

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:00:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1256860380  

@mhassanshafiq can this issue be closed then?

---

## Comment 26

> Username: mhassanshafiq  
> Created at: 2022-09-26 03:13:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1257417292  

@klemens-morgenstern The actual issue is not resolved. The above is just a way around it.  
Not sure if the team was able to reproduce it or work on a resolution.

---

## Comment 27

> Username: gopalak  
> Created at: 2022-09-26 14:41:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1258145060  

Yeah I also changed code to use tcp::socket instead of stream. Its kind of hard to reproduce. As part of another issue we are working on a not so small version to see if we can simulate the load and find the scenarios. At most I will try to send a boost handler trace once I get to finish this.

---

## Comment 28

> Username: terrywh  
> Created at: 2023-04-24 12:03:28 UTC  
> Updated at: 2023-04-24 12:13:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1520023862  

i encounter a similar issue: under heavy load, when the timer actually fires (some backend server not able to handle request correctly causing timeouts), and the sigabrt may trigger sometimes.  
  
  
when i made one the following changes:  
  
1.  stop reusing socket from tcp_stream.  
2. disable all the "expires_after / expires_never"   
  
and the problems go away.

---

## Comment 29

> Username: gopalak  
> Created at: 2023-07-14 03:04:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1635198902  

@madmongo1 @mhassanshafiq This issue showed up again - just to give some context -  
1. There is just 1 IO thread for the context and multiple connections on it (ssl)  
2. This also seems to be triggered under load when one connection just installs a read handler, and from the stack I see the socket got like 4 bytes most likely a close frame of some sort   
  
This is my understanding of the stack trace. I see close op being called and triggers an assert the same way the assert hits on wr_impl.is_locked check - clearly is not MT issue here.  
  
There are some very sensitive scenarios where I see this happening. The same code with same scenario with less load doesnt trigger this assert   
  
I understand its very difficult to go by just the symptoms we are stating here but there is some code path in the close.hpp which is clearly very delicate but would be great if this can be checked and fixed. Its like a time bomb ticking and I dont think its actually a beast stream issue but some logic in the close code path IMHO  
  
Cheers

---

## Comment 30

> Username: klemens-morgenstern  
> Created at: 2023-07-14 07:29:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2345#issuecomment-1635425445  

WDYM by "installs a read handler" - are you initializing multiple ops at once?
