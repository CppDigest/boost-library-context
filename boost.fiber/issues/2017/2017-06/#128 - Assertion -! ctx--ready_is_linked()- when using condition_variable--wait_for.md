# #128 - Assertion "! ctx->ready_is_linked()" when using condition_variable::wait_for [Closed]

> Username: m0xf  
> Created at: 2017-06-02 19:39:36 UTC  
> Updated at: 2017-06-03 08:16:12 UTC  
> Closed at: 2017-06-03 08:16:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/128  

When i use condition_variable::wait_for in multipliy fibers program crashes with assertion:  
"Assertion failed: ! ctx->ready_is_linked(), file libs\fiber\src\scheduler.cpp, line 192".   
  
  
Minimal sample:  
```c++  
#include <boost/fiber/all.hpp>  
#include <mutex>  
#include <thread>  
#include <chrono>  
  
int main()  
{  
    boost::fibers::condition_variable condvar;  
    boost::fibers::mutex mutex;  
  
    auto do_work = [&]()  
    {  
        while (true)  
        {  
            {  
                std::unique_lock<boost::fibers::mutex> lock(mutex);  
                condvar.wait_for(lock, std::chrono::milliseconds(50));  
            }  
            //emulate some calculations  
            std::this_thread::sleep_for(std::chrono::milliseconds(5));  
        }  
    };  
  
    boost::fibers::fiber fiber1(do_work);  
    boost::fibers::fiber fiber2(do_work);  
    boost::fibers::fiber fiber3(do_work);  
    boost::fibers::fiber fiber4(do_work);  
    boost::fibers::fiber fiber5(do_work);  
    boost::fibers::fiber fiber6(do_work);  
  
    boost::fibers::fiber fiber([&]()  
    {  
        while (true)  
        {  
            {  
                std::lock_guard<boost::fibers::mutex> lock(mutex);  
                condvar.notify_one();  
            }  
            boost::this_fiber::sleep_for(std::chrono::milliseconds(10));  
        }  
    });  
    fiber.join();  
    return 0;  
}  
```  
Compiler msvc-14.0, windows 7.

---

## Comment 1

> Username: olk  
> Created at: 2017-06-03 08:16:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/128#issuecomment-305960118  

ty - fixed
