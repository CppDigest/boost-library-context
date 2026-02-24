# #260 - Assert on (! shutdown_), function schedule_from_remote [Open]

> Username: dmitryikh  
> Created at: 2020-10-11 20:43:35 UTC  
> Updated at: 2020-10-11 20:43:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/260  

While investigating another case just found this assert fired.  
Boost fiber from develop branch.  
  
The toy example to reproduce (some time it needs several runs):  
  
```cpp  
#include <atomic>  
#include <thread>  
#include <chrono>  
#include <iostream>  
#include <boost/fiber/all.hpp>  
  
using namespace std::chrono_literals;  
  
int main()  
{  
    boost::fibers::mutex mtx;  
    boost::fibers::condition_variable cond;  
    std::atomic<bool> stopping;  
  
    std::vector<std::thread> pool;  
    for (size_t i = 0; i < 10; i++)  
    {  
        auto th = std::thread([&] ()  
        {  
            boost::fibers::use_scheduling_algorithm<boost::fibers::algo::shared_work>();  
            auto f1 = boost::fibers::fiber([&] () {  
                int i = 0;  
                while (!stopping.load())  
                {  
                    std::cout << "trying to get lock" << std::endl;  
                    std::unique_lock l(mtx);  
                    std::this_thread::sleep_for(100ms);  
                    std::cout << i++ << " waiting" << std::endl;  
                    cond.wait_for(l, 15ms);  
                    std::cout << "worker" << std::endl;  
                }  
            });  
            f1.join();  
        });  
        pool.push_back(std::move(th));  
    }  
  
    auto th2 = std::thread([&] ()  
    {  
        while (!stopping.load())  
        {  
            {  
                std::unique_lock l(mtx);  
                cond.notify_all();  
            }  
            std::this_thread::sleep_for(1ms);  
        }  
    });  
  
    std::this_thread::sleep_for(200ms);  
    stopping.store(true);  
  
    for (auto& th : pool)  
    {  
        th.join();  
    }  
    th2.join();  
  
    return 0;  
}  
```  
  
possible output:  
```  
trying to get lock  
trying to get lock  
trying to get lock  
trying to get lock  
trying to get lock  
trying to get lock  
trying to get lock  
trying to get lock  
trying to get lock  
trying to get lock  
0 waiting  
0 waiting  
0 waiting  
worker  
0 waiting  
0 waiting  
0 waiting  
0 waiting  
Assertion failed: (! shutdown_), function schedule_from_remote, file libs/fiber/src/scheduler.cpp, line 227.  
```
