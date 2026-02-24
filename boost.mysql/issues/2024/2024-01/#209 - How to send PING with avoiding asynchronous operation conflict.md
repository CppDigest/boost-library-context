# #209 - How to send PING  with avoiding asynchronous operation conflict ? [Closed]

> Username: redboltz  
> Created at: 2024-01-17 08:47:00 UTC  
> Updated at: 2024-01-17 13:00:20 UTC  
> Closed at: 2024-01-17 11:27:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/209  

# Question  
I want to keep mysql connection. The mysql server could have keep alive timeout, so  if the client send nothing then the connection is disconnected by the server.  
In order to avoid that I tried to use [async_ping](https://www.boost.org/doc/libs/1_84_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/async_ping/overload1.html).  
(I'm developping asynchronous application)  
  
I use async_ping with `boost::asio::steady_timer`.  
So far, there is no problem.  
  
However, when I call [async_prepare_statement()](https://www.boost.org/doc/libs/1_84_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/async_prepare_statement/overload1.html) and then call [async_execute()](https://www.boost.org/doc/libs/1_84_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/async_execute/overload1.html), sometimes I got an assertion failed at the following line:  
  
https://github.com/boostorg/mysql/blob/boost-1.84.0/include/boost/mysql/impl/internal/channel/message_writer.hpp#L125  
  
```cpp  
        BOOST_ASSERT(!done());  
```  
  
I read the document carefully and found [Single outstanding operation per connection](https://www.boost.org/doc/libs/1_84_0/libs/mysql/doc/html/mysql/overview.html#mysql.overview.async.single_outstanding_operation_per).  
  
In the failed case, the program ran as follows:  
  
1. call async_ping() and the CompletionToken is not invoked yet.  
2. call async_prepare_statement()  
  
Apparently, step2 violate the rule.  
  
Is there any good way to avoid this problem?  
  
I think that the following approach doesn't satisfy my requirement:  
1. Remove async_ping() and steady_timer.   
2. If async_prepare_statement() or async_execute() CompletionToken returens the error by keep alive timeout by server, then reconnect.   
This approach works fine but in this retry case takes a long time. This is one reason that I want to keep the connection.  
  
# Environment  
- Boost 1.84.0  
- clang++ 16.0.6  
- Linux 6.6.10

---

## Comment 1

> Username: anarthal  
> Created at: 2024-01-17 10:42:46 UTC  
> Updated at: 2024-01-17 10:43:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/209#issuecomment-1895544726  

Hi @redboltz,  
  
Your diagnostic is correct, running two asynchronous operations in parallel results in undefined behavior, since messages from the two operations will interleave when read/written from the socket, causing confusion.  
  
The easiest solution is to protect your connection using an asynchronous mutex. If you're familiar with Python, the concept is similar to [`asyncio.Lock`](https://docs.python.org/3/library/asyncio-sync.html#asyncio.Lock). Your mutex will be acquired before calling `async_ping` and `async_prepare_statement` (and any other `async_xxx` function you may be calling), then released when the operation is complete. If a mutex is tried to be acquired when an operation is already in progress, the task will wait until the mutex is released.  
  
Now, for the hard part: `std::mutex` is *not* suitable for this. There is no built-in primitive to achieve this, but it can be done with Asio channels (they're experimental, but work well). Sample implementation:  
  
```cpp  
#include <boost/asio/any_io_executor.hpp>  
#include <boost/asio/experimental/channel.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <boost/system/detail/error_code.hpp>  
#include <boost/system/error_code.hpp>  
  
#include <chrono>  
#include <cstdio>  
  
namespace asio = boost::asio;  
using boost::system::error_code;  
  
class async_mutex  
{  
    // This channel will be used to implement the mutex  
    asio::experimental::channel<void(error_code)> chan_;  
  
public:  
    // The channel uses a buffer size of 1. The first async_send will complete  
    // immediately, filling the buffer and effectively locking the mutex. Successive  
    // async_send operations will be queued until the buffered message is read.  
    // This is what unlock does.  
    async_mutex(asio::any_io_executor ex) : chan_(std::move(ex), 1) {}  
  
    // Most I/O objects provide a get_executor() function  
    asio::any_io_executor get_executor() { return chan_.get_executor(); }  
  
    // If you're in C++11, you'll need to use decltype() to specify the return type  
    template <class CompletionToken>  
    auto async_lock(CompletionToken&& token)  
    {  
        return chan_.async_send(error_code(), std::forward<CompletionToken>(token));  
    }  
  
    // Effectively unlock the mutex by reading a message. If the mutex is locked,  
    // ok will always be true  
    void unlock()  
    {  
        bool ok = chan_.try_receive([](error_code) {});  
        assert(ok);  
        (void)ok;  
    }  
};  
  
int main()  
{  
    asio::io_context ctx;  
    asio::steady_timer tim(ctx), tim2(ctx);  
    async_mutex mtx(ctx.get_executor());  
  
    mtx.async_lock([&](error_code) {  
        printf("Locked 1\n");  
        tim.expires_after(std::chrono::seconds(1));  
        tim.async_wait([&](error_code) {  
            printf("Unlock 1\n");  
            mtx.unlock();  
        });  
    });  
  
    mtx.async_lock([&](error_code) {  
        printf("Locked 2\n");  
        tim2.expires_after(std::chrono::seconds(1));  
        tim2.async_wait([&](error_code) {  
            printf("Unlock 2\n");  
            mtx.unlock();  
        });  
    });  
  
    ctx.run();  
}  
```  
  
Boost 1.85 will introduce experimental support for `connection_pool` (preview [here](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/connection_pool.html)), which takes care of pinging for you.  
  
Please let me know if this solved your issue.  
  
Ruben.

---

## Comment 2

> Username: redboltz  
> Created at: 2024-01-17 11:27:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/209#issuecomment-1895616355  

@anarthal , thank you for the response. It works perfectly!  
As you mentioned `std::mutex` is not suitable. `std::mutex` requires the same thread lock/unlock, but async_mutex doesn't.  
I ran `ctx.run();` multiple threads, async_mutex still works fine.   
Also I test async_mutex with coroutine like `co_await mtx.async_lock(boost::asio::deferred);`. Of course it works fine.  
If doesn't block any threads, it is nice.  
  
At first, I would try to my own operation queing mechanism but statement binding parameter type is various, so implementation would seem to be complecated. So I stopped it and ask here. I saved my time, thanks!  
  
Also I'm looking forward to the next release. connection_pool seems to be nice.  
  
async_mutex can be used for general purpose. That's good to know.  
It is related but off topic, do you know any way to implement or existing implementation of async_shared_mutex ?  
I mean async equivalant of `std::shared_mutex`, like async_mutex with `std::mutex`.

---

## Comment 3

> Username: redboltz  
> Created at: 2024-01-17 12:52:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/209#issuecomment-1895749998  

> I ran ctx.run(); multiple threads, async_mutex still works fine.  
  
I guess that it works fine with `asio::experimental::channel` is by chance.  
  
I noticed that if I call `ctx.run();` multiple threads as follows, I should use `asio::experimental::concurrent_channel` instead of `asio::experimental::channel`.  
`  
  
```cpp  
    std::vector<std::thread> ths;  
    ths.reserve(8);  
    for (int i = 0; i != 8; ++i) {  
        ths.emplace_back(  
            [&] {  
                ctx.run();  
            }  
        );  
    }  
  
    for (auto& th : ths) th.join();  
```  
  
Anyway, the original issue has been solved.

---

## Comment 4

> Username: anarthal  
> Created at: 2024-01-17 12:59:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/209#issuecomment-1895762599  

I was writing exactly that. Also beware that connections don't implement any thread-safety on their own. This means you need to make sure that no two completion handlers run in parallel for the same connection.  
  
I *think* you should be safe with just a `concurrent_channel`. But it depends on the particular impl you're doing. You may need to protect your calls with a strand.  
  
I advise you to write q stress test with thread sanitizer enabled (`-fsanitize=thread`), as this is the most reliable way to check for thread-safety, in my experience. I've found many things I had thought to be safe not to be.  
  
I'll come back to you about the shared mutex when I can.

---

## Comment 5

> Username: anarthal  
> Created at: 2024-01-17 13:00:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/209#issuecomment-1895764257  

BTW, have a look at `asio::thread_pool`. It's like `io_context`, but will create the threads for you.
