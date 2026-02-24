# #198 - Unfinished task is resumed after co_main returns [Open]

> Username: nyibbang  
> Created at: 2024-09-11 10:00:16 UTC  
> Updated at: 2024-11-29 01:01:04 UTC  
> Url: https://github.com/boostorg/cobalt/issues/198  

Hello,  
  
I've been using Cobalt with Boost 1.86.0, and I've had an issue with my code that caused it to segfault due to use of destroyed resources.  
  
I've reproduced my issue on a smaller sample code:  
  
```c++  
#include <boost/asio/error.hpp>  
#include <boost/cobalt.hpp>  
#include <boost/asio.hpp>  
#include <boost/system/system_error.hpp>  
#include <iostream>  
  
namespace cobalt = boost::cobalt;  
namespace asio = boost::asio;  
using namespace std::chrono_literals;  
  
int GLOBAL_FLAG = 0;  
  
struct set_global_flag {  
    set_global_flag() {  
        GLOBAL_FLAG = 1;  
    }  
  
    ~set_global_flag() {  
        GLOBAL_FLAG = 0;  
    }  
};  
  
cobalt::task<void> long_loop_task() {  
    auto exec = co_await cobalt::this_coro::executor;  
  
    auto timer = cobalt::use_op.as_default_on(asio::steady_timer(exec));  
    while (!co_await cobalt::this_coro::cancelled) {  
        std::cout << "long_loop: flag is " << GLOBAL_FLAG << std::endl;  
        try {  
            timer.expires_from_now(1s);  
            co_await timer.async_wait();  
        }  
        catch (const boost::system::system_error& err) {  
            if (err.code() == asio::error::operation_aborted) {  
                std::cout << "long_loop: operation aborted" << std::endl;  
            }  
        }  
    }  
}  
  
cobalt::main co_main(int, char**) {  
    auto sgf = set_global_flag();  
    auto llt = long_loop_task();  
  
    auto exec = co_await cobalt::this_coro::executor;  
    auto timeout_timer = cobalt::use_op.as_default_on(asio::steady_timer(exec));  
    timeout_timer.expires_from_now(50ms);  
  
    co_await cobalt::race(  
        llt,  
        timeout_timer.async_wait()  
    );  
    co_return 0;  
}  
```  
  
I build this code with g++13 & libstdc++ on Ubuntu 22.04.  
  
The output of this program is the following:  
```  
long_loop: flag is 1  
long_loop: flag is 0  
long_loop: flag is 0  
long_loop: flag is 0  
long_loop: flag is 0  
[...]  
```   
  
As we can see, the loop accesses the global flag after the `set_global_flag` object is destroyed (the flag is reset to 0), which means that the long_loop_task coroutine has been resumed after the co_main function returned. In this program, nothing much happens, but in my original code, the flag was a pointer passed by reference to the loop and the `set_global_flag` was a `unique_ptr`, which caused the loop to access through the reference an object that was destroyed.  
  
The program never terminates, even if an interrupt (Ctrl+C) or termination (SIGTERM) signal is sent. I have to kill it with a SIGKILL or SIGQUIT.  
  
I understand that according to `cobalt::race` specification, the loop task should not be cancelled, because it is passed by lvalue-reference. It is however surprising to me that it is resumed though, even after the task object was destroyed.  
  
Am I missing something ?  
Thank you in advance.

---

## Comment 1

> Username: neonxc  
> Created at: 2024-11-28 14:18:49 UTC  
> Updated at: 2024-11-28 14:19:17 UTC  
> Url: https://github.com/boostorg/cobalt/issues/198#issuecomment-2506231636  

I believe this is indeed an issue, caused by insufficiently destroyed coroutines. Please consider the following code:  
  
```c++  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
#include <iostream>  
  
namespace ba = boost::asio;  
namespace cobalt = boost::cobalt;  
  
template <typename T1, typename T2>  
cobalt::promise<void> delay(std::chrono::duration<T1, T2> d)  
{  
  ba::system_timer tim{co_await cobalt::this_coro::executor, d};  
  co_await tim.async_wait(cobalt::use_op);  
}  
  
using namespace std::chrono;  
cobalt::promise<void> co()  
{  
      
    struct s{~s(){std::cout << "det" << std::endl; }} so{};  
    co_await delay(5ms);  
}  
  
cobalt::main co_main(int argc, char * argv[])  
{  
    {  
        std::cout << "begin\n";  
        std::list<cobalt::promise<void>> l{};  
        l.emplace_back(delay(1ms));  
        l.emplace_back(co());  
          
        co_await race(l);  
    }  
    std::cout << "after\n";  
      
    co_return 0;  
}  
```   
  
expected output:  
  
> begin  
> det  
> after  
  
actual:  
  
> begin  
> after  
> det  
  
https://godbolt.org/z/PvonsEW3z  
  
thanks for taking care :)

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-11-29 00:51:08 UTC  
> Url: https://github.com/boostorg/cobalt/issues/198#issuecomment-2506897050  

@neonxc No, your code shows expected behavior. There isn't a thing like an asynchronous destructor, therefore the async operation gets cancelled, but since it's unawaited the exception will leak.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-11-29 01:01:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/198#issuecomment-2506903130  

@nyibbang The task destructor doesn't trigger a cancel - so using a promise will work.   
I did not add the cancel, because the task isn't meant to be used with race et. al., but I am not sure that's the correct or expected choice.   
So I will either need to remove the ability to detach the await or add that in the destructor. I am not sure yet.
