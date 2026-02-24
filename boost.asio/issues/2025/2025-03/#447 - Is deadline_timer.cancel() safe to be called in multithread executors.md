# #447 - Is deadline_timer.cancel() safe to be called in multithread executors? [Open]

> Username: GoldRobot  
> Created at: 2025-03-05 12:21:06 UTC  
> Updated at: 2025-03-05 12:26:12 UTC  
> Url: https://github.com/boostorg/asio/issues/447  

Hello!  
I failed to google that, or find exact answer in docs. Hope I can get an answer hee!  
  
So deadline_timer have this [note](https://beta.boost.org/doc/libs/1_82_0/doc/html/boost_asio/reference/deadline_timer.html#boost_asio.reference.deadline_timer.thread_safety):  
```  
Thread Safety  
Distinct objects: Safe.  
Shared objects: Unsafe.  
```  
My problem is, I can't understand if I (as lib's user) 'share' object with asio itself.  
Is code below safe in real world scenario when `timer->cancel()` can be called in one moment with timer expiration?  
```c++  
auto ioc = boost::asio::io_context();  
for (auto i = 0; i < 10000000; ++i)  
{  
    auto timer = std::make_shared<boost::asio::steady_timer>(ioc);  
    timer->expires_at(std::chrono::steady_clock::now());  
    timer->async_wait([timer](auto){/*exact work doesn't matter*/});  
    auto cancelation = [timer]() { timer->cancel(); };  
    boost::asio::post(ioc, cancelation);  
}  
auto pool_size = 100;  
auto pool = boost::asio::thread_pool(pool_size);  
for (auto i = 0; i < pool_size; i++) {boost::asio::post(pool, [&ioc](){ioc.run();});}  
```  
Or in words: Can I call `timer.cancel()` method from arbitary thread* if timer was scheduled using executor(io_context/thread_pool) serviced by multiple threads?  
\* only one timer.cancel() at time, ofcourse, because timer public methods aren't thread safe between each other.  
  
I ask that question because I want to schedule 'disposable' timers with option to cancel them without creating strand for each of such timers. Just using given executor.
