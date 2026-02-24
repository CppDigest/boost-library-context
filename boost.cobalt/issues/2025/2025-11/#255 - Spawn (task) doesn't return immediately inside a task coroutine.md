# #255 - Spawn (task) doesn't return immediately  inside a task coroutine [Closed]

> Username: YanzhaoW  
> Created at: 2025-11-17 23:01:09 UTC  
> Updated at: 2025-11-21 13:04:52 UTC  
> Closed at: 2025-11-21 13:04:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/255  

Hi,  
  
Sorry if this has been asked before.  
  
## Problem  
  
when spawning a task inside another task routine, even when `asio::detached` is used, the spawn function doesn't return immediately.  
  
## How to reproduce  
  
### Utility functions  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
#include <print>  
#include <thread>  
  
namespace asio = boost::asio;  
namespace cobalt = boost::cobalt;  
  
auto mutex = std::mutex{};  
  
auto get_ms_time_now() -> int  
{  
    static auto const start = std::chrono::steady_clock::now();  
    return (std::chrono::steady_clock::now() - start) / std::chrono::milliseconds(1);  
}  
  
template <typename... Args>  
void trace(std::format_string<Args...> fmt, Args&&... args)  
{  
    auto _ = std::scoped_lock{ mutex };  
    std::println("At time {} ms and thread {}: {}",  
                 get_ms_time_now(),  
                 std::this_thread::get_id(),  
                 std::format(fmt, std::forward<Args>(args)...));  
}  
  
```  
  
### Main logic:  
  
```cpp  
namespace  
{  
    auto test_task() -> cobalt::task<void>  
    {  
        trace("running test task ...");  
        std::this_thread::sleep_for(std::chrono::seconds{ 1 });  
        trace("test task finished ...");  
        co_return;  
    }  
    auto outer() -> cobalt::task<void>  
    {  
        auto executor = co_await cobalt::this_coro::executor;  
        trace(">>>>>>>> before testing task ...");  
        cobalt::spawn(executor, test_task(), asio::detached);  
        trace("<<<<<<<< after testing task ...");  
        co_return;  
    }  
} // namespace  
  
auto main() -> int  
{  
    auto io_context = asio::thread_pool{ 4 };  
    cobalt::spawn(io_context, outer(), asio::detached);  
    io_context.join();  
}  
```  
  
### output  
  
```text  
At time 0 ms and thread 139717766301376: >>>>>>>> before testing task ...  
At time 0 ms and thread 139717766301376: running test task ...  
At time 1000 ms and thread 139717766301376: test task finished ...  
At time 1000 ms and thread 139717766301376: <<<<<<<< after testing task ...  
```  
  
Here from the output, you can see that the spawn function actually blocks instead of returning immediately.  
  
Is there any way to make `spawn` function return immediately?  
  
Thanks

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-11-18 13:50:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/255#issuecomment-3547735949  

Spawn dispatches onto the executor, so no. Or at least not in a few lines of code.

---

## Comment 2

> Username: YanzhaoW  
> Created at: 2025-11-18 19:40:44 UTC  
> Url: https://github.com/boostorg/cobalt/issues/255#issuecomment-3549295857  

Ok, but even if I pass `asio::thread_pool` to the spawn, it still doesn't return immediately.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-11-19 16:53:08 UTC  
> Url: https://github.com/boostorg/cobalt/issues/255#issuecomment-3553733416  

Right, because you're calling `spawn` on the same executor. So what you're trying to achieve won't work with `spawn`.  
  
Cobalt is meant to be used in single threaded environment anyhow, so I am not sure what you're trying to achieve.

---

## Comment 4

> Username: YanzhaoW  
> Created at: 2025-11-21 13:04:39 UTC  
> Updated at: 2025-11-21 13:04:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/255#issuecomment-3562947793  

Ok, that's a pity. I like the interface of the cobalt, which is a much better than `asio::experimental::coro`. But it's a deal breaker for me if the library can only handle a single thread.  
  
Anyway, thanks for the info.
