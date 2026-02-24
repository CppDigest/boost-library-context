# #2775 - Compile error when any_io_executor based websocket stream with bind_executor strand [Open]

> Username: redboltz  
> Created at: 2023-12-15 06:50:11 UTC  
> Updated at: 2024-05-22 13:52:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2775  

### Version of Beast  
  
- My local:   
  - Boost 1.84.0,   
  - Beast 351  
- Godbolt  
  - Boost 1.83.0  
  - Beast 347  
  
### Steps necessary to reproduce the problem  
  
Compile the following code:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/beast/websocket/stream.hpp>  
  
namespace net = boost::asio;  
namespace bs = boost::beast;  
  
using tcp = net::basic_stream_socket<net::ip::tcp, net::any_io_executor>;  
using ws = bs::websocket::stream<tcp>;  
  
void tcp_test() {  
    net::io_context ioc;  
    net::any_io_executor exe = ioc.get_executor();  
  
    std::string buf;  
    tcp socket{exe};  
    auto str = net::make_strand(exe);  
    socket.async_write_some(  
        net::buffer(buf),   
        net::bind_executor( // on tcp socket with any_io_executor, no error happens  
            str,  
            [](auto, auto){}  
        )  
    );  
}  
  
void ws_test() {  
    net::io_context ioc;  
    net::any_io_executor exe = ioc.get_executor();  
  
    std::string buf;  
    ws web_socket{exe};  
    auto str = net::make_strand(exe);  
    web_socket.async_write(  
        net::buffer(buf),   
#if 1 // If you set 0 (remove bind_executor), then error is disappeared  
        net::bind_executor(  
            str,  
            [](auto, auto){}  
        )  
#else  
        [](auto, auto){}  
#endif  
    );  
}  
  
int main() {}  
```  
  
Got the following compile error:  
  
```  
explorer/libs/boost_1_83_0/boost/asio.hpp:188:  
/opt/compiler-explorer/libs/boost_1_83_0/boost/asio/strand.hpp:260:15: error: no member named 'on_work_finished' in 'boost::asio::any_io_executor'  
  260 |     executor_.on_work_finished();  
      |     ~~~~~~~~~ ^  
```  
  
  
godbolt link(x86-64 clang 17.0.1) : https://godbolt.org/z/nocj654EK  
  
### All relevant compiler information  
- x86-64 clang 17.0.1   
- x86-64 gcc 13.2  
  
  
### What I noticed  
- `tcp` doesn't cause the error.  
- The error happens only the combination of `ws` with `bind_executor` strand.  
  -  `ws` contains `tcp` as the next layer. `tcp` has `any_io_executor`.  
  - The strand `str` is created by `make_strand` and `any_io_executor` `exe` is passed.

---

## Comment 1

> Username: ashtum  
> Created at: 2023-12-15 08:12:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-1857455675  

The issue is associated with creating a work_guard from a `strand<any_io_executor>`. It attempts to utilize `on_work_started()` and `on_work_finished()` functions, which are not defined for `asio::any_io_executor`. This results in a compile error in `websocket::stream<tcp>::async_write` because it attempts to create a work_guard from the associated executor.  
It can be reproduced with the following code as well: https://godbolt.org/z/nvr9jjnrY  
```C++  
#include <boost/asio.hpp>  
  
namespace asio = boost::asio;  
  
int main()  
{  
    asio::io_context ioc;  
    asio::any_io_executor exec = ioc.get_executor();  
    auto strand = asio::make_strand(exec);  
    auto wg = asio::make_work_guard(strand);  
}  
```  
I will further investigate to determine whether we can address this in Beast or if it is a bug in Asio.

---

## Comment 2

> Username: redboltz  
> Created at: 2023-12-15 08:18:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-1857463894  

I'm not 100% sure but passing `strand.get_inner_executor()` to `make_work_guard` could solve the issue ?  
  
```cpp  
#include <boost/asio.hpp>  
  
namespace asio = boost::asio;  
  
int main()  
{  
    asio::io_context ioc;  
    asio::any_io_executor exec = ioc.get_executor();  
    auto strand = asio::make_strand(exec);  
    auto wg = asio::make_work_guard(strand.get_inner_executor());  
}  
```  
  
https://godbolt.org/z/jv1zEW7ET

---

## Comment 3

> Username: ashtum  
> Created at: 2023-12-15 09:07:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-1857527661  

> I'm not 100% sure but passing `strand.get_inner_executor()` to `make_work_guard` could solve the issue ?  
  
This will generate a work guard for `any_io_executor` instead of `strand<any_io_executor>`. Calling [get_executor](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/executor_work_guard/get_executor.html) on this work guard will return an instance of `any_io_executor`.  
Although I believe it will function correctly in this case, as we don't retrieve the executor from the work guard, I am seeking a cleaner solution or addressing this in Asio.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-12-15 13:01:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-1857848696  

I've raised an issue in Asio for this; let's see what will be the response. https://github.com/chriskohlhoff/asio/issues/1394

---

## Comment 5

> Username: redboltz  
> Created at: 2024-01-11 14:56:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-1887356832  

https://github.com/chriskohlhoff/asio/issues/1394 still no response. It seems that there are a lot of uncommented issues in the project.  
I think that this issue is often appered using C++20 coroutine with strand in the real world.   
See https://godbolt.org/z/7EnWa3Es1  
If there were no update until the next release, I would appliciate if you implemented some workaround on the beast side .

---

## Comment 6

> Username: ashtum  
> Created at: 2024-01-11 16:48:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-1887563328  

Yeah, Chris usually doesn't respond to the issues, but he reads them. We should track the changes in the repository to see if it gets fixed or not.

---

## Comment 7

> Username: redboltz  
> Created at: 2024-04-16 11:17:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-2058845626  

Boost 1.85.0 has been released. Any updates about the issue?

---

## Comment 8

> Username: ashtum  
> Created at: 2024-04-16 12:26:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-2058969825  

No, unfortunately, this issue, along with many others, hasn't been addressed in the Asio for 1.85.0 release.

---

## Comment 9

> Username: redboltz  
> Created at: 2024-05-22 13:20:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-2124783870  

I noticed that I used strand in an unusual way; my library no longer uses strand like that. Here is another proof-of-concept (PoC) code example based on the code I initially reported:  
  
https://godbolt.org/z/1q6vxzfja  
  
```cpp  
        net::bind_executor(  
            net::any_io_executor{str}, // no error, strand still effects  
            [](auto, auto){}  
        )  
```  
  
My issue has been solved, but I leave it up to you whether to close the issue or not.

---

## Comment 10

> Username: ashtum  
> Created at: 2024-05-22 13:47:20 UTC  
> Updated at: 2024-05-22 13:52:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2775#issuecomment-2124845032  

Thanks for reporting this.  
  
The root cause of the issue is creating a `work_guard` from a `strand<any_io_executor>` because Asio chose the wrong specialization. Yes, if we wrap that strand in an `any_completion_executor` or `any_io_executor`, the problem disappears because now we are creating a `work_guard` from these types, not the strand itself. The downside is it creates an extra layer of indirection, which I would say is insignificant in practice.  
  
I would suggest keeping this issue open as this is more of a workaround and the root cause still exists.
