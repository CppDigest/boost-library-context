# #798 - Boost ASIO multithreaded program which shares a single IO context hangs [Open]

> Username: rohitpai  
> Created at: 2023-08-24 15:15:37 UTC  
> Updated at: 2023-08-24 17:34:35 UTC  
> Url: https://github.com/boostorg/boost/issues/798  

I am using boost version 1.79 on embedded linux 5.15 platform which is running on Dual Core Cortex A7 hardware.   
I have the below code which I am testing to check sharing same IO context with multiple threads (which execute io.run)   
  
  
```  
#include <boost/asio.hpp>  
#include <iostream>  
#include <iomanip>  
  
namespace asio = boost::asio;  
using namespace std::chrono_literals;  
using boost::system::error_code;  
  
static std::atomic_int tid_gen = 0;  
thread_local int const tid     = [] { return ++tid_gen; }();  
static constexpr auto  now = std::chrono::steady_clock::now;  
static auto const      start   = now();  
static std::mutex console_mx;  
  
void trace(auto const&... msg) {  
    std::lock_guard lk(console_mx);  
    std::cerr << "at " << std::setw(8) << (now() - start)/1ms << "ms - tid:" << tid << " ";  
    (std::cerr << ... << msg) << std::endl;  
}  
  
void worker(asio::io_context& ioContext) {  
    trace("Worker thread enter");  
    ioContext.run(); // Run the io_context to handle asynchronous operations  
    trace("Worker thread exit");  
}  
  
int main() {  
    try {  
        asio::io_context ioContext;  
  
        asio::steady_timer task1(ioContext, 100ms);  
        asio::steady_timer task2(ioContext, 200ms);  
  
        task1.async_wait([](error_code ec) {  
            trace("Start Task1: ", ec.message());  
            if (!ec)  
                sleep(6);  
        });  
  
        task2.async_wait([](error_code ec) {  
            trace("Start Task2: ", ec.message());  
            if (!ec)  
                sleep(12);  
        });  
  
        // Create a work object to prevent ioContext.run() from returning immediately  
        auto work = make_work_guard(ioContext);  
  
        // Create multiple worker threads  
        std::vector<std::thread> threads;  
        for (int i = 0; i < 2; ++i) {  
            threads.emplace_back(worker, std::ref(ioContext));  
        }  
  
  
        trace("App started :", std::thread::hardware_concurrency());  
  
        work.reset();  
        // Join the worker threads  
        for (auto& thread : threads) {  
            thread.join();  
        }  
  
        trace("All worker threads joined.");  
    } catch (std::exception const& e) {  
        trace("Exception: ", std::quoted(e.what()));  
    }  
}  
```  
Output   
  
```  
root@hgx:~# /usr/bin/progress-code  
at        0ms - tid:1 Worker thread enter  
at        1ms - tid:2 App started :2  
at        2ms - tid:3 Worker thread enter  
at      100ms - tid:1 Start Task1: Success  
at     6100ms - tid:1 Start Task2: Success  
at    18100ms - tid:1 Worker thread exit  
  
```  
The program never terminates and need to be killed. One worker thread seem to hang and only one worker thread is picking up both timer callbacks serially.    
I am not able to understand the behavior.  As per this [article](https://theboostcpplibraries.com/boost.asio-scalability) both the threads should run concurrently and pickup the handlers.   
Why one worker thread executing  ioContext.run() always hangs in the above example. Need your help to understand if its a bug in the library or something wrong in the test code.   
  
GDB back trace of the thread that hangs   
  
```  
#0  0x76cf5ce4 in pause () from /lib/libc.so.6  
#1  0x0040a0c0 in boost::asio::detail::null_event::do_wait () at /home/ropai/SDK_GROUP/sysroots/armv7ahf-vfpv4d16-openbmc-linux-gnueabi/usr/include/boost/asio/detail/impl/null_event.ipp:47  
#2  boost::asio::detail::null_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (this=<optimized out>) at /home/ropai/SDK_GROUP/sysroots/armv7ahf-vfpv4d16-openbmc-linux-gnueabi/usr/include/boost/asio/detail/null_event.hpp:82  
#3  boost::asio::detail::conditionally_enabled_event::wait (this=<optimized out>, lock=...) at /home/ropai/SDK_GROUP/sysroots/armv7ahf-vfpv4d16-openbmc-linux-gnueabi/usr/include/boost/asio/detail/conditionally_enabled_event.hpp:97  
#4  boost::asio::detail::scheduler::do_run_one (this=this@entry=0x42d128, lock=..., this_thread=..., ec=...) at /home/ropai/SDK_GROUP/sysroots/armv7ahf-vfpv4d16-openbmc-linux-gnueabi/usr/include/boost/asio/detail/impl/scheduler.ipp:501  
#5  0x004056c8 in boost::asio::detail::scheduler::run (ec=..., this=0x42d128) at /home/ropai/SDK_GROUP/sysroots/armv7ahf-vfpv4d16-openbmc-linux-gnueabi/usr/include/boost/asio/detail/impl/scheduler.ipp:210  
#6  boost::asio::io_context::run (this=<optimized out>) at /home/ropai/SDK_GROUP/sysroots/armv7ahf-vfpv4d16-openbmc-linux-gnueabi/usr/include/boost/asio/impl/io_context.ipp:63  
#7  worker (ioContext=...) at ../progress_code_main.cpp:182  
```  
  
If I start both the timers at the same time without any delay then both the threads pick up the handlers and exit correctly.   
  
  
changes  
```  
        asio::steady_timer task1(ioContext, 1ms);  
        asio::steady_timer task2(ioContext, 1ms);  
```  
  
Output   
```  
at        4ms - tid:1 App started :2  
at       18ms - tid:2 Worker thread enter  
at       26ms - tid:2 Start Task1: Success  
at       31ms - tid:3 Worker thread enter  
at       32ms - tid:3 Start Task2: Success  
at    12033ms - tid:3 Worker thread exit  
at    12033ms - tid:2 Worker thread exit  
at    12034ms - tid:1 All worker threads joined.  
```
