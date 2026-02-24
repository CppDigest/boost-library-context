# #703 - co_spawn core!!!!!! [Closed]

> Username: jbl19860422  
> Created at: 2022-11-02 05:44:35 UTC  
> Updated at: 2022-11-02 05:50:35 UTC  
> Closed at: 2022-11-02 05:50:34 UTC  
> Url: https://github.com/boostorg/boost/issues/703  

the program is very simple:  
just co_spawn call twice, one is in main, the other is in co_routine,  
and the main sleep  
but this simple program core,i don't know why this happen.  
  
i test 4 cases  
  
1. first case's code:  
  
```  
#include <iostream>  
#include <atomic>  
  
#include <boost/asio.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/shared_ptr.hpp>  
#include <boost/asio/steady_timer.hpp>  
  
using namespace std::literals::chrono_literals;  
int main(int argc, char *argv[])  
{  
    std::shared_ptr<boost::asio::io_context> io = std::make_shared<boost::asio::io_context>();  
    boost::asio::co_spawn(*io, [io]()->boost::asio::awaitable<void> {  
        boost::asio::co_spawn(*io, [io]()->boost::asio::awaitable<void> {  
            std::cout << "conroutine start running" << std::endl;  
            std::cout << "conroutine end" << std::endl;  
        }, boost::asio::detached);  
    }, boost::asio::detached);  
    io->run();  
    sleep(100000000);  
    return 0;  
}  
```  
  
after run, the output is:  
```  
conroutine start running  
conroutine end  
Segmentation fault (core dumped)  
```  
  
2. second case, i  let the outter coroutine wait a timer:  
  
```  
#include <iostream>  
#include <atomic>  
  
#include <boost/asio.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/shared_ptr.hpp>  
#include <boost/asio/steady_timer.hpp>  
  
using namespace std::literals::chrono_literals;  
int main(int argc, char *argv[])  
{  
    std::shared_ptr<boost::asio::io_context> io = std::make_shared<boost::asio::io_context>();  
    boost::asio::co_spawn(*io, [io]()->boost::asio::awaitable<void> {  
        boost::asio::co_spawn(*io, [io]()->boost::asio::awaitable<void> {  
            std::cout << "conroutine start running" << std::endl;  
            std::cout << "conroutine end" << std::endl;  
        }, boost::asio::detached);  
        // add 2s time delay  
        boost::asio::steady_timer timer(*io);  
        timer.expires_after(2s);  
        co_await timer.async_wait(boost::asio::use_awaitable);  
    }, boost::asio::detached);  
    io->run();  
    sleep(100000000);  
    return 0;  
}  
```  
it also core!!!  
after run, the output is:  
```  
conroutine start running  
conroutine end  
Segmentation fault (core dumped)  
```  
3. the third case, i add a time delay in inner coroutine:  
```  
#include <iostream>  
#include <atomic>  
  
#include <boost/asio.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/shared_ptr.hpp>  
#include <boost/asio/steady_timer.hpp>  
  
using namespace std::literals::chrono_literals;  
int main(int argc, char *argv[])  
{  
    std::shared_ptr<boost::asio::io_context> io = std::make_shared<boost::asio::io_context>();  
    boost::asio::co_spawn(*io, [io]()->boost::asio::awaitable<void> {  
        boost::asio::co_spawn(*io, [io]()->boost::asio::awaitable<void> {  
            std::cout << "conroutine start running" << std::endl;  
  
            boost::asio::steady_timer timer(*io);  
            timer.expires_after(2s);  
            co_await timer.async_wait(boost::asio::use_awaitable);  
              
            std::cout << "conroutine end" << std::endl;  
        }, boost::asio::detached);  
    }, boost::asio::detached);  
    io->run();  
    sleep(100000000);  
    return 0;  
}  
```  
it also core:  
after run the output is:  
```  
conroutine start running  
Segmentation fault (core dumped)  
```  
it seems that the inner coroutine is not run ok, the "coroutine end" is not printed.  
  
4. i add a delay in either coroutine:  
```  
#include <iostream>  
#include <atomic>  
  
#include <boost/asio.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/shared_ptr.hpp>  
#include <boost/asio/steady_timer.hpp>  
  
using namespace std::literals::chrono_literals;  
int main(int argc, char *argv[])  
{  
    std::shared_ptr<boost::asio::io_context> io = std::make_shared<boost::asio::io_context>();  
    boost::asio::co_spawn(*io, [io]()->boost::asio::awaitable<void> {  
        boost::asio::co_spawn(*io, [io]()->boost::asio::awaitable<void> {  
            std::cout << "conroutine start running" << std::endl;  
  
            boost::asio::steady_timer timer(*io);  
            timer.expires_after(2s);  
            co_await timer.async_wait(boost::asio::use_awaitable);  
  
            std::cout << "conroutine end" << std::endl;  
        }, boost::asio::detached);  
  
        boost::asio::steady_timer timer(*io);  
        timer.expires_after(3s);  
        co_await timer.async_wait(boost::asio::use_awaitable);  
    }, boost::asio::detached);  
    io->run();  
    sleep(100000000);  
    return 0;  
}  
```  
this time, it don't core,the output is:  
```  
conroutine start running  
conroutine end  
```  
  
  
  
  
  
  
------------------  
**when core is generated, the coreinfo is:  
it seems that "caler_ = caler" cause a segment fault:**  
  
  
this is the core detail:  
```  
b#0  0x000055a48462904d in boost::asio::detail::awaitable_frame_base<boost::asio::any_io_executor>::push_frame (this=0x0, caller=0x7f1448000c90)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/impl/awaitable.hpp:439  
t439        caller_ = caller;  
[Current thread is 1 (Thread 0x7f14570ef700 (LWP 3663641))]  
(gdb)   
(gdb)   
(gdb) bt  
#0  0x000055a48462904d in boost::asio::detail::awaitable_frame_base<boost::asio::any_io_executor>::push_frame (this=0x0, caller=0x7f1448000c90)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/impl/awaitable.hpp:439  
#1  0x000055a484641650 in boost::asio::awaitable<void, boost::asio::any_io_executor>::await_suspend<boost::asio::detail::awaitable_thread_entry_point> (this=0x7f1448000e00, h=...)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/awaitable.hpp:106  
#2  0x000055a4846816bb in boost::asio::detail::_ZN5boost4asio6detail20co_spawn_entry_pointINS0_15any_io_executorEZZ4mainENKUlvE_clEvEUlvE_NS1_16detached_handlerEEENS0_9awaitableINS1_28awaitable_thread_entry_pointET_EEPNS7_IvS9_EES9_T0_T1_.actor(boost::asio::detail::_ZN5boost4asio6detail20co_spawn_entry_pointINS0_15any_io_executorEZZ4mainENKUlvE_clEvEUlvE_NS1_16detached_handlerEEENS0_9awaitableINS1_28awaitable_thread_entry_pointET_EEPNS7_IvS9_EES9_T0_T1_.frame *) (frame_ptr=0x7f1448000c80) at /home/jbl19860422/code/mms/build/include/boost/asio/impl/co_spawn.hpp:160  
#3  0x000055a484615fdb in std::__n4861::coroutine_handle<void>::resume (this=0x7f1448000c90) at /usr/include/c++/11/coroutine:126  
#4  0x000055a4846443ae in boost::asio::detail::awaitable_frame_base<boost::asio::any_io_executor>::resume (this=0x7f1448000c90)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/impl/awaitable.hpp:456  
#5  0x000055a484641d1d in boost::asio::detail::awaitable_thread<boost::asio::any_io_executor>::pump (this=0x7f14570edf90)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/impl/awaitable.hpp:714  
#6  0x000055a48464d5ba in boost::asio::detail::awaitable_handler<boost::asio::any_io_executor>::operator() (this=0x7f14570edf90)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/impl/use_awaitable.hpp:73  
#7  0x000055a48464ea04 in boost::asio::detail::binder0<boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> >::operator() (this=0x7f14570edf90)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/detail/bind_handler.hpp:60  
#8  0x000055a484652b2d in boost::asio::asio_handler_invoke<boost::asio::detail::binder0<boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> > > (function=...)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/handler_invoke_hook.hpp:88  
#9  0x000055a484652223 in boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder0<boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> >, boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> > (function=..., context=...) at /home/jbl19860422/code/mms/build/include/boost/asio/detail/handler_invoke_helpers.hpp:54  
#10 0x000055a484651b32 in boost::asio::detail::asio_handler_invoke<boost::asio::detail::binder0<boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> >, boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> > (function=..., this_handler=0x7f14570edf90) at /home/jbl19860422/code/mms/build/include/boost/asio/detail/bind_handler.hpp:111  
#11 0x000055a484650d74 in boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder0<boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> >, boost::asio::detail::binder0<boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> > > (function=..., context=...)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/detail/handler_invoke_helpers.hpp:54  
#12 0x000055a48464ebcb in boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<boost::asio::detail::awaitable_handler<boost::asio::any_io_executor> >, std::allocator<void> > (base=0x55a4867fe3e0, call=true) at /home/jbl19860422/code/mms/build/include/boost/asio/detail/executor_function.hpp:116  
#13 0x000055a484616d71 in boost::asio::detail::executor_function::operator() (this=0x7f14570ee120) at /home/jbl19860422/code/mms/build/include/boost/asio/detail/executor_function.hpp:64  
#14 0x000055a484640074 in boost::asio::asio_handler_invoke<boost::asio::detail::executor_function> (function=...)  
    at /home/jbl19860422/code/mms/build/include/boost/asio/handler_invoke_hook.hpp:88  
```

---

## Comment 1

> Username: jbl19860422  
> Created at: 2022-11-02 05:50:34 UTC  
> Url: https://github.com/boostorg/boost/issues/703#issuecomment-1299610981  

my mistake, the lambda should call "co_return;" anyway
