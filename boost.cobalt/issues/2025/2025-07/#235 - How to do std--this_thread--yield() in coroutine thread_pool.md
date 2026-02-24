# #235 - How to do std::this_thread::yield() in coroutine thread_pool? [Closed]

> Username: JaydenFish  
> Created at: 2025-07-09 17:08:42 UTC  
> Updated at: 2025-12-26 15:40:33 UTC  
> Closed at: 2025-07-10 13:03:00 UTC  
> Url: https://github.com/boostorg/cobalt/issues/235  

```C++  
#include <fmt/core.h>                                                                                                                                              
  
#include <boost/asio/as_tuple.hpp>  
#include <boost/asio/experimental/concurrent_channel.hpp>  
#include <boost/asio/redirect_error.hpp>  
#include <boost/asio/steady_timer.hpp>                                                                                                                             
#include <boost/asio/strand.hpp>                                                                                                                                   
#include <boost/asio/this_coro.hpp>  
#include <boost/asio/thread_pool.hpp>  
#include <boost/cobalt.hpp>  
#include <boost/cobalt/op.hpp>                                                                                                                                     
  
namespace cobalt = boost::cobalt;  
using boost::system::error_code;  
  
cobalt::task<int> cpu_intense_work(                                                                                                                                
    int a,  
    int b)                                                                                                                                                         
{  
    // co_await boost::asio::steady_timer{co_await boost::asio::this_coro::executor, std::chrono::milliseconds(0)}.async_wait(cobalt::use_op);  // this can work!  
    std::this_thread::sleep_for(std::chrono::milliseconds(1));                                                                                                     
    fmt::println("{} {}", a, b);                                                                                                                                   
    co_return a;                                                                                                                                                   
}  
  
cobalt::task<void> work(int id)                                                                                                                                    
{  
    for (int a = 0; a <= 4; a++)                                                                                                                                   
    {  
        int c = co_await cpu_intense_work(id, a);  // I want new task to be at the tail of thread_poop's queue, and task to be done by first in first out          
    }  
}  
int main(int, char *[])  
{  
    const std::size_t        n = 2u;                                                                                                                               
    boost::asio::thread_pool tp{n};  
  
    std::list<std::future<void>> futs;                                                                                                                             
    for (int id = 0; id < 3; ++id)                                                                                                                                 
        futs.emplace_back(cobalt::spawn(tp.get_executor(), work(id), boost::asio::use_future));                                                                    
                                                                                                                                                                   
    for (auto &fut : futs)                                                                                                                                         
    {                                                                                                                                                              
        fut.get();                                                                                                                                                 
    }                                                                                                                                                              
    return 0;  
}  
```  
I want the new task `cpu_intense_work(id,a)` to be at the tail of thread_pool's queue. I found `co_await timer` in above code can do it, it act just like `std::this_thread::yield()` in thead. But I think it's not a good way for coroutine.  
How to do?  
for example, right now the output is:  
```  
0 0  
1 0  
0 1  
1 1  
0 2  
1 2  
1 3  
0 3  
1 4  
0 4  
2 0  
2 1  
2 2  
2 3  
2 4  
```  
I want:  
```  
0 0  
1 0  
2 0  
0 1  
1 1  
2 1  
0 2  
1 2  
2 2  
1 3  
2 3  
0 3  
1 4  
2 4  
0 4  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-07-10 06:24:44 UTC  
> Url: https://github.com/boostorg/cobalt/issues/235#issuecomment-3055787897  

```cpp  
co_await asio::post(co_await cobalt::this_coro::executor);  
```  
  
Will suspend the current coroutine and post the resumption to the end of the thread_pool queue.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-07-10 06:42:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/235#issuecomment-3055848570  

Also be aware that what you're doing is not supported by cobalt, because your coroutines might switch threads - cobalt is single threaded.   
  
You can use an `asio::strand` to make the completion sequential, e.g.:  
  
```cpp  
    for (int id = 0; id < 3; ++id)                                                                                                                                 
        futs.push_back(cobalt::spawn(asio::make_strand(tp.get_executor()), work(id), boost::asio::use_future));                                                                    
```  
  
Now `work` will run on a single thread within the thread pool.   
  
See here for details: https://www.boost.org/doc/libs/master/doc/html/boost_asio/overview/core/strands.html  
  
If you want a thread-safe coroutine type you'll need to resort to `asio::awaitable`.

---

## Comment 3

> Username: JaydenFish  
> Created at: 2025-07-10 09:20:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/235#issuecomment-3056513244  

Is it `co_await boost::asio::post(boost::cobalt::use_op);`?  
your code compile fail.  
  
> co_await asio::post(co_await cobalt::this_coro::executor);  
>   
> Will suspend the current coroutine and post the resumption to the end of the thread_pool queue.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2025-07-10 10:28:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/235#issuecomment-3056820227  

Same thing, seems you are on an older boost version.

---

## Comment 5

> Username: RUSLoker  
> Created at: 2025-12-26 15:38:23 UTC  
> Updated at: 2025-12-26 15:40:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/235#issuecomment-3693027915  

> co_await asio::post(co_await cobalt::this_coro::executor);  
> Will suspend the current coroutine and post the resumption to the end of the thread_pool queue.  
  
@klemens-morgenstern why don't you add this thing as `auto boost::cobalt::yield()` or something like this? I think it would be more convenient for library users. So one don't need to think about how does it works on low level and keep code clean.
