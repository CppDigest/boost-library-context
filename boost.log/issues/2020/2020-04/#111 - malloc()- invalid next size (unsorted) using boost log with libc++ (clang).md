# #111 - malloc(): invalid next size (unsorted) using boost log with libc++ (clang) [Closed]

> Username: j-silver  
> Created at: 2020-04-01 22:09:31 UTC  
> Updated at: 2024-04-06 09:58:22 UTC  
> Closed at: 2020-04-01 22:35:06 UTC  
> Url: https://github.com/boostorg/log/issues/111  

Not sure whether it is a bug of Boost.Log, of libc++ or clang, or Asio (...or mine).  
  
I'm trying to run a modified version of the async daytime server from the tutorials of the Asio library. This is my version (essentially the same using lambdas instead of boost::bind and a few other changes). Apologies if it is too long, however the essential part is in the callback **handle_write**, where BOOST_LOG is used, and in **async_write**:  
  
```  
#include <string>  
#include <boost/asio.hpp>  
#include <boost/log/sources/global_logger_storage.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/utility/manipulators/add_value.hpp>  
#include <date/date.h>  
#include <memory>  
#include <iostream>  
  
  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(my_logger, boost::log::sources::logger_mt)  
  
using boost::asio::ip::tcp;  
  
std::string make_daytime_string()  
{  
    auto now {date::floor<std::chrono::microseconds>(std::chrono::system_clock::now())};  
    return date::format("%F %T", now);  
}  
  
  
class tcp_connection : public std::enable_shared_from_this<tcp_connection> {  
    tcp::socket _socket;  
    std::string _message;  
  
public:  
    using pointer = std::shared_ptr<tcp_connection>;  
  
    static pointer create(boost::asio::io_context& ioContext)  
    {  
        return std::shared_ptr<tcp_connection>(new tcp_connection {ioContext});  
    }  
  
    tcp::socket& socket()  
    {  
        return _socket;  
    }  
  
    void start()  
    {  
        _message = make_daytime_string();  
  
         boost::asio::async_write(_socket,  
                                 boost::asio::buffer(_message),  
                                 // callback  
                                 [](auto const& error_code, size_t bytes)  
                                 {  
                                      handle_write(error_code, bytes);  
                                 });  
    }  
  
private:  
    // private constructor  
    explicit tcp_connection(boost::asio::io_context& ioContext)  
        : _socket {ioContext}, _message {}  
    {}  
  
    // callback function  
    static void handle_write(boost::system::error_code const err, size_t bytes)  
    {  
        auto& lg {my_logger::get()};  
        if (err.failed())  
            BOOST_LOG(lg) << err.message();  
        else  
            BOOST_LOG(lg) << std::to_string(bytes) << " bytes written";  
    }  
};  
  
  
class tcp_server {  
    tcp::acceptor _acceptor;  
  
public:  
    explicit tcp_server(boost::asio::io_context& ioContext)  
        : _acceptor {ioContext, tcp::endpoint {tcp::v4(), 13333}}  
    {  
        start_accept();  
    }  
  
private:  
    void start_accept()  
    {  
        auto newConnection {tcp_connection::create(_acceptor.get_executor().context())};  
  
        _acceptor.async_accept(newConnection->socket(),  
                               [this, newConnection](auto const& error_code)  
                               {  
                                    handle_accept(newConnection, error_code);  
                               });  
    }  
  
    void handle_accept(tcp_connection::pointer newConn, boost::system::error_code const& e)  
    {  
        if (! e.failed())  
            newConn->start();  
  
        start_accept();  
    }  
};  
  
  
int main()  
{  
    try {  
        boost::asio::io_context ioContext;  
        tcp_server server {ioContext};  
        ioContext.run();  
    }  
    catch (std::exception& e) {  
        std::cerr << e.what() << '\n';  
    }  
  
    return 0;  
}  
```  
  
If I compile it with clang++ 9.0.1 (on Fedora 31)  
  
```  
clang++ -Wall -Wextra -Wpedantic -Wconversion -Weffc++ -std=c++17 -c async_daytime_server.cpp -DBOOST_LOG_DYN_LINK=1 -stdlib=libc++ -g3  
  
clang++ -o async_daytime_server async_daytime_server.o -lboost_log -pthread -lc++  
```  
  
When interrogated with a daytime client, for example [the same as in the Boost tutorial](https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/tutorial/tutdaytime1/src.html) but with port 13333, the server crashes when executing the callback _handle_write_:  
  
**malloc(): invalid next size (unsorted)**  
  
```  
Process 49155 (async_daytime_s) of user 1000 dumped core.  
          
        Stack trace of thread 49155:  
        #0  0x00007fd3869ac625 raise (libc.so.6)  
        #1  0x00007fd3869958d9 abort (libc.so.6)  
        #2  0x00007fd3869f04af __libc_message (libc.so.6)  
        #3  0x00007fd3869f7a9c malloc_printerr (libc.so.6)  
        #4  0x00007fd3869faa6c _int_malloc (libc.so.6)  
        #5  0x00007fd3869fc1c4 malloc (libc.so.6)  
        #6  0x00007fd3869ef22e __vasprintf_internal (libc.so.6)  
        #7  0x00007fd3869c7f9a __asprintf (libc.so.6)  
        #8  0x00007fd3869a4944 __assert_fail_base (libc.so.6)  
        #9  0x00007fd3869a4a66 __assert_fail (libc.so.6)  
```  
  
Any idea of what could be the reason?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-01 22:35:06 UTC  
> Url: https://github.com/boostorg/log/issues/111#issuecomment-607523324  

In `tcp_connection::start` you don't seem to save a `shared_ptr` to the `tcp_connection` object. This is necessary because `async_write` that you call in `tcp_connection::start` returns before it consumes `_message`, and it will also operate asynchronously on `_socket`, both of which are likely destroyed at that point.  
  
Also, as a general advice, you may want to run your code under `valgrind`, which will highlight any errors with memory accesses, including use-after-free.

---

## Comment 2

> Username: j-silver  
> Created at: 2020-04-02 19:10:31 UTC  
> Url: https://github.com/boostorg/log/issues/111#issuecomment-608050556  

Hi @Lastique. Thank you for your answer.  
  
I don't think it's a problem of lifetime of the `tcp_connection` object. Yes, when I try to think about the execution flow it could be that, but still it doesn't explain the behaviour. Maybe it's not an issue with Boost Log, but I'm still convinced it's related to libc++. In this case I could report it to the libc++ team.  
  
What I should have mentioned in my opening post is that this server prints the datetime anyway, on the first connection initiated by the client, even if it crashes immediately after.   
And it crashes when attempting to use the log.  
  
In fact the programs works fine, without crashing, if compiled with gcc 9 or even with clang 9 if linked against GNU libstdc++. This problem arises only when I link with libc++.  
  
Initially I thought: maybe the tutorial is correct and my modifications introduce a bug, maybe boost::shared_ptr is a different beast from std::shared_ptr, maybe a lambda cannot replace boost::bind in this case. But that tutorial also crashes when `handle_write` is implemented in the same way as in my example, when trying to write to the log.  
  
In fact, even if I make the write non-async, even when I modify `tcp_connection::start()` in this way  
  
```  
void start()  
{  
        _message = make_daytime_string();  
        auto bytes {boost::asio::write(_socket,  
                                 boost::asio::buffer(_message))};  
                                 // callback  
//                                 [](auto const& error_code, size_t bytes)  
//                                 {  
//                                      handle_write(error_code, bytes);  
//                                 });  
        auto& lg {my_logger::get()};  
        BOOST_LOG(lg) << std::to_string(bytes) << " bytes written";  
        auto& lg {my_logger::get()};  
        if (err.failed())  
            BOOST_LOG(lg) << err.message();  
        else  
            BOOST_LOG(lg) << std::to_string(bytes) << " bytes written";  
 }  
```  
(there is no async execution, here, no deferred callback) even in this situation the program crashes when arrives at   
`  
BOOST_LOG(lg) << std::to_string(bytes) << " bytes written"  
`  
(and the daytime is correctly delivered to the client)  
  
Any idea? Should I reach out to libc++ instead?

---

## Comment 3

> Username: Lastique  
> Created at: 2020-04-02 19:26:38 UTC  
> Url: https://github.com/boostorg/log/issues/111#issuecomment-608058351  

I would suggest:  
  
1. Run the code under `valgrind`. Truly, this tool may point you to the problem right away.  
2. Try modifying the code in various ways in order to narrow down the problematic part. For example:  
2.1. Try replacing `to_string(bytes)` with just `bytes` (i.e. so that the integer is formatted by the output stream).  
2.2. Try replacing `BOOST_LOG(lg)` with `std::cout` and removing other Boost.Log stuff.  
2.3. Try removing Boost.ASIO stuff, especially the asynchronous stuff. Basically, simplify as much as possible.  
  
You will need to narrow down your reproducer anyway in order to report the problem to libc++/clang devs. In the meantime you may find the problem source elsewhere.

---

## Comment 4

> Username: j-silver  
> Created at: 2020-04-02 21:16:56 UTC  
> Url: https://github.com/boostorg/log/issues/111#issuecomment-608093979  

I tried with valgrind but, maybe I'm wrong, I couldn't find any hint that the async was the root of the problem (nor the lifetime of the pointer). However, I will go to libc++/clang devs because this:  
  
```  
#include <boost/log/sources/global_logger_storage.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/utility/manipulators/add_value.hpp>  
  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(my_logger, boost::log::sources::logger_mt)  
  
int main()  
{  
    auto& lg {my_logger::get()};  
    BOOST_LOG(lg) << "Hello, World!";  
}  
```  
is the simplest code I can imagine. And it fails. With the same error. With libc++/clang.

---

## Comment 5

> Username: sharpMouse  
> Created at: 2024-04-06 09:25:26 UTC  
> Url: https://github.com/boostorg/log/issues/111#issuecomment-2041029154  

It look like that you have wrong Boost binaries.  
  
If you get Boost via Conan, do not forget to use "-s compiler.libcxx=libstdc++11" parameter.

---

## Comment 6

> Username: j-silver  
> Created at: 2024-04-06 09:58:21 UTC  
> Url: https://github.com/boostorg/log/issues/111#issuecomment-2041035781  

Wow, I had completely forgotten this dabbling of mine with Asio, during the first lockdown of 2020. I don't even remember if I wrote to the clang devs.  
  
@sharpMouse  I think I was using boost from Fedora, but in the meantime my interests have moved a little bit elsewhere, although I still use C++ at work everyday.  
Thanks for your reply.
