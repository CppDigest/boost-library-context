# #234 - Why this coredump? [Closed]

> Username: JaydenFish  
> Created at: 2025-07-06 20:38:30 UTC  
> Updated at: 2025-07-09 13:25:32 UTC  
> Closed at: 2025-07-09 13:25:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234  

env: boost1.84 clang20.1.7 -O3  
the example `thread_pool.cpp` with a little change `std::string res = fmt::format("{}", a + b); ` in `cpu_intense_work`  
it coredump at line `for::format`.  
  
```C++  
// Copyright (c) 2023 Klemens D. Morgenstern  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
/// This example shows how to use threads to offload cpu_intense work.  
#include <fmt/std.h>                                                                                                           
  
#include <boost/asio/as_tuple.hpp>  
#include <boost/asio/experimental/concurrent_channel.hpp>                                                                      
#include <boost/asio/redirect_error.hpp>                                                                                       
#include <boost/asio/strand.hpp>  
#include <boost/asio/this_coro.hpp>  
#include <boost/asio/thread_pool.hpp>  
#include <boost/cobalt.hpp>                                                                                                    
  
namespace cobalt = boost::cobalt;  
using boost::system::error_code;  
  
// this is a function doing some CPU heavy work that should be offloaded onto a thread_pool  
cobalt::promise<int> cpu_intense_work(  
    int a,                                                                                                                     
    int b,                                                                                                                     
    boost::asio::executor_arg_t = {},                                                                                          
    cobalt::executor            = cobalt::this_thread::get_executor())                                                         
// ^set the executor manually. but default it so we can still use it with the thread_local one if present                      
{  
    std::string res = fmt::format("{}", a + b);                                                                                
    co_return a + b;                                                                                                           
}  
  
cobalt::task<void> work(int min_a, int max_a, int b)  
{  
    auto exec = co_await cobalt::this_coro::executor;                                                                          
    for (int a = min_a; a <= max_a; a++)                                                                                       
    {                                                                                                                          
        // the following two calls offload the work to another thread.                                                         
        int c = co_await cpu_intense_work(a, b, boost::asio::executor_arg, exec);                                              
        printf("The CPU intensive result of adding %d to %d, is %d\n", a, b, c);                                               
    }                                                                                                                          
}  
int main(int, char *[])                                                                                                        
{  
    const std::size_t        n = 4u;                                                                                           
    boost::asio::thread_pool tp{n};                                                                                            
  
    // a very simple thread pool                                                                                               
  
    auto cpl =                                                                                                                 
        [](std::exception_ptr ep) {                                                                                            
            if (ep)                                                                                                            
                try                                                                                                            
                {                                                                                                              
                    std::rethrow_exception(ep);                                                                                
                }                                                                                                              
                catch (std::exception &e)                                                                                      
                {                                                                                                              
                    printf("Completed with exception %s\n", e.what());                                                         
                }                                                                                                              
        };                                                                                                                     
  
    cobalt::spawn(boost::asio::make_strand(tp.get_executor()), work(0, 10, 32), cpl);                                          
    cobalt::spawn(boost::asio::make_strand(tp.get_executor()), work(10, 20, 22), cpl);                                         
    cobalt::spawn(boost::asio::make_strand(tp.get_executor()), work(50, 60, -18), cpl);                                        
  
    // wait them so they don't leak.                                                                                           
    tp.join();                                                                                                                 
    return 0;                                                                                                                  
}                                                                                                                              
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-07-07 02:35:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3043310591  

I ran the code on a clang++20 & gcc in release mode and couldn't reproduce the error.   
You'll need to provide the actual code-dump / stack trace.

---

## Comment 2

> Username: JaydenFish  
> Created at: 2025-07-07 07:34:52 UTC  
> Updated at: 2025-07-07 07:39:08 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3043809739  

my env is clang=20.1.7 os=fedora42 ,  ( boost1.84 and fmt11.1.4 )installed by conda:  
I compiled the code with:  
```  
clang++  -fPIC -march=x86-64 -O0 -g -std=c++23 -pipe -Wall   -DNDEBUG   -c -o bin/test_cobalt.o main/test_cobalt.cpp  
clang++  -Wl,-O1 -Wl,--sort-common -Wl,-x -Wl,-X -Wl,--enable-new-dtags -Wl,--as-needed -Wl,--hash-style=gnu -Wl,--exclude-libs=ALL -Wl,--no-undefined -z now   bin/test_cobalt.o    -lboost_cobalt -lfmt   -o bin/test_cobalt  
```  
and run `gdb bin/test_cobalt`, backtrace is:  
  
<img width="3749" height="1299" alt="Image" src="https://github.com/user-attachments/assets/13f0fc39-1598-4b01-bac9-89ebf9134489" />

---

## Comment 3

> Username: JaydenFish  
> Created at: 2025-07-07 07:39:39 UTC  
> Updated at: 2025-07-07 07:40:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3043822482  

I've downgrade fmt to 11.0.2, the stacktrace changed:  
  
<img width="3745" height="737" alt="Image" src="https://github.com/user-attachments/assets/4816602f-83eb-4962-b8bf-ab5786e0db67" />  
  
Weird, is it a bug with fmt? But I use this fmt for a long time.

---

## Comment 4

> Username: JaydenFish  
> Created at: 2025-07-07 09:40:18 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3044201807  

I just tested it on another machine with arch, boost1.88, fmt11.1.4(conda), it worked well.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-07-07 10:22:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3044350277  

I don't think it's fmt, it's probably something wrong with the function inlinling in the coroutine frame or something of that sort. E.g. that the arguments `a` and `b` point to invalid memory.   
I suspect the error would stop if you upgraded clang.

---

## Comment 6

> Username: JaydenFish  
> Created at: 2025-07-07 19:24:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3046287990  

I solved this problem by compile and install cobalt. the boost1.84 installed by conda is a piece of shit.

---

## Comment 7

> Username: JaydenFish  
> Created at: 2025-07-08 16:41:31 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3049622927  

I have another similar problem. It coredump again! In `std::cout << conf << std::endl;`  
I don't think this bug is about yaml-cpp, because the `std::cout << conf` is correct.  
  
```C++  
// Copyright (c) 2023 Klemens D. Morgenstern  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
/// This example shows how to use threads to offload cpu_intense work.  
#include <iostream>  
#include <yaml-cpp/yaml.h>  
  
#include <boost/asio/as_tuple.hpp>  
#include <boost/asio/experimental/concurrent_channel.hpp>  
#include <boost/asio/redirect_error.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/asio/this_coro.hpp>  
#include <boost/asio/thread_pool.hpp>  
#include <boost/cobalt.hpp>  
  
namespace cobalt = boost::cobalt;  
using boost::system::error_code;  
  
// this is a function doing some CPU heavy work that should be offloaded onto a thread_pool  
cobalt::promise<int> cpu_intense_work(  
    int a,  
    int b,  
    const YAML::Node &conf,  
    boost::asio::executor_arg_t = {},  
    cobalt::executor            = cobalt::this_thread::get_executor())  
// ^set the executor manually. but default it so we can still use it with the thread_local one if present  
{  
    std::cout << conf << std::endl; // coredump  
    co_return a + b;  
}  
  
cobalt::task<void> work(int min_a, int max_a, int b, const YAML::Node &conf)  
{  
    auto exec = co_await cobalt::this_coro::executor;  
    for (int a = min_a; a <= max_a; a++)  
    {  
        // the following two calls offload the work to another thread.  
        int c = co_await cpu_intense_work(a, b, conf, boost::asio::executor_arg, exec);  
        printf("The CPU intensive result of adding %d to %d, is %d\n", a, b, c);  
    }  
}  
int main(int, char *[])  
{  
    const std::size_t        n = 4u;  
    boost::asio::thread_pool tp{n};  
  
    // a very simple thread pool  
  
    auto cpl =  
        [](std::exception_ptr ep) {  
            if (ep)  
                try  
                {  
                    std::rethrow_exception(ep);  
                }  
                catch (std::exception &e)  
                {  
                    printf("Completed with exception %s\n", e.what());  
                }  
        };  
    YAML::Node conf=YAML::Load("\  
Simulator:\n\  
  lat_offset:\n\  
  - 28000000\n\  
  - 3000000\n\  
  - 10000000\n\  
  lat_stat_threshold: 3e6\n\  
  Account: \n\  
    acc_id: 1 \n\  
    init: &id001 25.0e4 \n\  
    avail: *id001 \n\   
    cash: *id001 \n\  
    Position: \n\  
TraderHelper: \n\  
  t0: 1 \n\  
  sec_order_limit: 295 \n\  
  fall_behind_thd: 10\n\  
  max_lose_ratio: -0.01 \n\  
  max_instr_lose: -50 \n\  
  Account: \n\  
    acc_id: 1 \n\   
    stat_id: 0 \n\   
    trader_id: 0 \n\  
    buy_comm_rate: \n\   
    - 5.0e-5 # SH \n\  
    - 7.5e-5 # SZ \n\  
    - 5.0e-5 \n\  
    sell_comm_rate: \n\  
    - 5.0e-5 \n\  
    - 7.5e-5 \n\  
    - 5.0e-5 \n\  
    order_min_comm: \n\  
    - 0.1 \n\  
    - 0.1 \n\  
    - 0.1 \n\  
    quota_cash: 9e9 \n\  
Trader: \n\  
  spread_threshold: 0.003 \n\  
  cancel_threshold: 0.003 \n\  
  max_pos: 120 \n\  
  order_qty: 60");  
  
    std::cout << conf << std::endl;  // correct  
    std::cout << YAML::Clone(conf) << std::endl;  // correct  
  
    cobalt::spawn(boost::asio::make_strand(tp.get_executor()), work(0, 10, 32, YAML::Clone(conf)), cpl);  
    cobalt::spawn(boost::asio::make_strand(tp.get_executor()), work(10, 20, 22, YAML::Clone(conf)), cpl);  
    cobalt::spawn(boost::asio::make_strand(tp.get_executor()), work(50, 60, -18, YAML::Clone(conf)), cpl);  
  
    // wait them so they don't leak.  
    tp.join();  
    return 0;  
}  
```  
  
<img width="3751" height="1646" alt="Image" src="https://github.com/user-attachments/assets/da8b6d85-53c9-44e7-84fa-4d1446197d10" />

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2025-07-09 10:12:09 UTC  
> Updated at: 2025-07-09 10:12:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3052019996  

Conf goes put of scope, pass it by value.

---

## Comment 9

> Username: JaydenFish  
> Created at: 2025-07-09 13:25:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/234#issuecomment-3052671604  

thanks
