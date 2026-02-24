# #259 - Assert on: ctx->remote_ready_is_linked() on cond_var.wait_for(..) [Open]

> Username: dmitryikh  
> Created at: 2020-10-09 12:06:14 UTC  
> Updated at: 2020-10-10 17:51:25 UTC  
> Url: https://github.com/boostorg/fiber/issues/259  

Situation:  
0. I use boost 1.69  
1. Have one thread pool (TP) with shared_work algo started on it.  
2. fibers running in TP is waiting on cond_var:  
```cpp  
	boost::fibers::mutex _mtx;  
	boost::fibers::condition_variable _condition;  
...  
  
  
	std::unique_lock lock{ _mtx };  
	if (!_condition.wait_for(lock, WaitTimeout(), GetWaitPredicate()))  
		throw std::runtime_error{ __FUNCTION__": pontentially lost" };  
  
```  
2. I Have other threads (not in thread pool, round_robin default scheduler). Where I notify cv:  
```cpp  
	std::unique_lock lock{ _mtx };  
	// fullfill predicate somehow:  
	_condition.notify_all();  
```  
  
If I build the program with assertions I got very fast the assert:  
```cpp  
void  
scheduler::sleep2ready_() noexcept {  
    // move context which the deadline has reached  
    // to ready-queue  
    // sleep-queue is sorted (ascending)  
    std::chrono::steady_clock::time_point now = std::chrono::steady_clock::now();  
    sleep_queue_type::iterator e = sleep_queue_.end();  
    for ( sleep_queue_type::iterator i = sleep_queue_.begin(); i != e;) {  
        context * ctx = & ( * i);  
        // dipatcher context must never be pushed to sleep-queue  
        BOOST_ASSERT( ! ctx->is_context( type::dispatcher_context) );  
        BOOST_ASSERT( main_ctx_ == ctx || ctx->worker_is_linked() );  
        BOOST_ASSERT( ! ctx->ready_is_linked() );  
#if ! defined(BOOST_FIBERS_NO_ATOMICS)  
        BOOST_ASSERT( ! ctx->remote_ready_is_linked() );   <---- THIS ASSERT  
#endif  
...  
```  
  
As far As I understand the fiber can be waked up by timer or by notification from another thread and this is not allowed by this assert. How can I fix the issue?  
  
**P.S. When I changed wait_for() -> wait() the assert is gone!**  
  
Thanks!

---

## Comment 1

> Username: dmitryikh  
> Created at: 2020-10-10 17:51:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/259#issuecomment-706587165  

I was able to extract minimal example that reproduces the assert:  
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
  
    auto th1 = std::thread([&] ()  
    {  
        auto f1 = boost::fibers::fiber([&] () {  
            int i = 0;  
            while (!stopping.load())  
            {  
                std::cout << "trying to get lock" << std::endl;  
                std::unique_lock l(mtx);  
                std::cout << i++ << " waiting" << std::endl;  
                cond.wait_for(l, 15ms);  
                std::cout << "worker" << std::endl;  
            }  
        });  
  
        auto f2 = boost::fibers::fiber([&] () {  
            int i = 0;  
            while (!stopping.load())  
            {  
                std::cout << "trying to get lock" << std::endl;  
                std::unique_lock l(mtx);  
                std::cout << i++ << " waiting" << std::endl;  
                cond.wait_for(l, 15ms);  
                std::cout << "worker" << std::endl;  
            }  
        });  
  
        f1.join();  
        f2.join();  
    });  
  
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
  
    auto th3 = std::thread([&] ()  
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
  
    std::cout << "Press any key" << std::endl;  
    std::cin.get();  
    stopping.store(true);  
  
    th1.join();  
    th2.join();  
    th3.join();  
  
    return 0;  
}  
```  
  
  
possible output:  
```  
...  
298 waiting  
worker  
trying to get lock  
290 waiting  
Assertion failed: (! ctx->remote_ready_is_linked()), function sleep2ready_, file libs/fiber/src/scheduler.cpp, line 87.  
[1]    53769 abort      bin/test_case  
...
