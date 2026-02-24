# #167 - Crash when use spawn to other executor combined with cobalt::run and custom awaitable [Closed]

> Username: Pele44  
> Created at: 2024-03-29 10:40:06 UTC  
> Updated at: 2024-04-01 13:26:15 UTC  
> Closed at: 2024-03-31 23:25:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/167  

When using boost::cobalt I experience nearly 100% crashes due to memory corruption. The simplest case that suffers from the mentioned problem is presented below:  
```c++  
//main.cpp  
  
#include <thread>  
  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
  
using namespace boost::asio;  
using namespace boost::cobalt;  
  
using namespace std::chrono_literals;  
  
namespace  
{  
struct ContextRunningOnSeparateThread  
{  
  ContextRunningOnSeparateThread()  
    : executorWorkGuard(make_work_guard(ioContext)), contextThread([this]() { ioContext.run(); })  
  {  
  }  
  
  ~ContextRunningOnSeparateThread() { executorWorkGuard.reset(); }  
  
  io_context ioContext;  
  executor_work_guard<decltype(ioContext)::executor_type> executorWorkGuard;  
  std::jthread contextThread;  
};  
  
class SimpleAwaitable  
{  
public:  
  bool await_ready() const noexcept { return true; }  
  
  bool await_suspend(std::coroutine_handle<> /*handle*/)  
  {  
    return false;  
  }  
  
  void await_resume() {}  
};  
  
task<void> coroThatShouldBeRunInSeparateThread()  
{  
  co_await SimpleAwaitable{};  
}  
  
promise<void> taskSpawningCoroToSeparateThread(auto& context)  
{  
  co_await spawn(context, coroThatShouldBeRunInSeparateThread(), use_op);  
}  
} // namespace  
  
int main(int /*argc*/, char** /*argv*/)  
{  
  ContextRunningOnSeparateThread contextHolder;  
  
  auto test = [](auto& context) -> task<void>  
  {  
    steady_timer timer{co_await boost::cobalt::this_coro::executor, 0ns};  
    co_await left_race(taskSpawningCoroToSeparateThread(context), timer.async_wait(use_op));  
  };  
  
  run(test(contextHolder.ioContext));  
}  
```  
  
My dev env:  
```  
g++ --version  
g++ (GCC) 13.2.1 20240316 (Red Hat 13.2.1-7)  
```  
  
I've recompiled boost (1.84.0) with address sanitizer and build:  
`g++ -I/home/zbigniewkocon/EDSLibs/gcc_13_2/boost_1_84_address_sanitizer/include/boost-1_84/ -L/home/zbigniewkocon/EDSLibs/gcc_13_2/boost_1_84_address_sanitizer/lib -O0 -fPIC -fsanitize=address -g -std=c++20 main.cpp -lboost_cobalt-gcc13-mt-d-x64-1_84 -lboost_system-gcc13-mt-d-x64-1_84`  
  
After run:  
```  
./a.out   
AddressSanitizer:DEADLYSIGNAL  
=================================================================  
==49609==ERROR: AddressSanitizer: SEGV on unknown address 0x000000000000 (pc 0x000000000000 bp 0x7f6c7adff6a0 sp 0x7f6c7adff688 T1)  
==49609==Hint: pc points to the zero page.  
==49609==The signal is caused by a READ memory access.  
==49609==Hint: address points to the zero page.  
    #0 0x0  (<unknown module>)  
    #1 0x427cd7 in boost::cobalt::detail::forward_dispatch_cancellation::operator()(boost::asio::cancellation_type) const::{lambda()#1}::operator()() const (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x427cd7) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #2 0x4576e3 in boost::asio::detail::binder0<boost::cobalt::detail::forward_dispatch_cancellation::operator()(boost::asio::cancellation_type) const::{lambda()#1}>::operator()() (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x4576e3) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #3 0x457b01 in void boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<boost::cobalt::detail::forward_dispatch_cancellation::operator()(boost::asio::cancellation_type) const::{lambda()#1}>, std::allocator<void> >(boost::asio::detail::executor_function::impl_base*, bool) (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x457b01) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #4 0x415c70 in boost::asio::detail::executor_function::operator()() (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x415c70) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #5 0x458d2d in boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x458d2d) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #6 0x41ce4e in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x41ce4e) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #7 0x4243ce in boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x4243ce) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #8 0x42379f in boost::asio::detail::scheduler::run(boost::system::error_code&) (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x42379f) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #9 0x42535d in boost::asio::io_context::run() (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x42535d) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
    #10 0x4069c1 in operator() /home/zbigniewkocon/repo/cobalt_bug/main.cpp:17  
    #11 0x411288 in __invoke_impl<void, (anonymous namespace)::ContextRunningOnSeparateThread::ContextRunningOnSeparateThread()::<lambda()> > /usr/include/c++/13/bits/invoke.h:61  
    #12 0x41124b in __invoke<(anonymous namespace)::ContextRunningOnSeparateThread::ContextRunningOnSeparateThread()::<lambda()> > /usr/include/c++/13/bits/invoke.h:96  
    #13 0x410b75 in _M_invoke<0> /usr/include/c++/13/bits/std_thread.h:292  
    #14 0x410b49 in operator() /usr/include/c++/13/bits/std_thread.h:299  
    #15 0x410b2d in _M_run /usr/include/c++/13/bits/std_thread.h:244  
    #16 0x7f6c7e0e33c2 in execute_native_thread_routine (/lib64/libstdc++.so.6+0xe33c2) (BuildId: 135b17851e2f57f305c48a54822dfd08cdaf060e)  
  
AddressSanitizer can not provide additional info.  
SUMMARY: AddressSanitizer: SEGV (<unknown module>)   
Thread T1 created by T0 here:  
    #0 0x7f6c7e448956 in pthread_create (/lib64/libasan.so.8+0x48956) (BuildId: 2b657470ea196ba4342e3bd8a3cc138b1e200599)  
    #1 0x7f6c7e0e3498 in std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) (/lib64/libstdc++.so.6+0xe3498) (BuildId: 135b17851e2f57f305c48a54822dfd08cdaf060e)  
    #2 0x40ba52 in _S_create<(anonymous namespace)::ContextRunningOnSeparateThread::ContextRunningOnSeparateThread()::<lambda()> > /usr/include/c++/13/thread:261  
    #3 0x407930 in jthread<(anonymous namespace)::ContextRunningOnSeparateThread::ContextRunningOnSeparateThread()::<lambda()> > /usr/include/c++/13/thread:149  
    #4 0x406ace in ContextRunningOnSeparateThread /home/zbigniewkocon/repo/cobalt_bug/main.cpp:17  
    #5 0x407733 in main /home/zbigniewkocon/repo/cobalt_bug/main.cpp:56  
    #6 0x7f6c7de46149 in __libc_start_call_main (/lib64/libc.so.6+0x28149) (BuildId: 7ea8d85df0e89b90c63ac7ed2b3578b2e7728756)  
    #7 0x7f6c7de4620a in __libc_start_main_impl (/lib64/libc.so.6+0x2820a) (BuildId: 7ea8d85df0e89b90c63ac7ed2b3578b2e7728756)  
    #8 0x4068c4 in _start (/home/zbigniewkocon/repo/cobalt_bug/a.out+0x4068c4) (BuildId: 3081e1baec47d68fb18e3f0c9dafcd0a5d8442b7)  
  
==49609==ABORTING  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-03-29 14:14:34 UTC  
> Url: https://github.com/boostorg/cobalt/issues/167#issuecomment-2027304687  

Thanks for reporting! That's a race condition unrelated to the custom awaitable. I don't know if the fix will make it into the upcoming release though.

---

## Comment 2

> Username: Pele44  
> Created at: 2024-03-29 14:25:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/167#issuecomment-2027318321  

I see. Are there any workarounds? Does any specific situation cause this error, i.e. what patterns should I avoid?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-03-29 14:29:04 UTC  
> Url: https://github.com/boostorg/cobalt/issues/167#issuecomment-2027322114  

It's the cancellation that left_select triggers that has the race condition. I don't know how your use-case can be changed to avoid this.

---

## Comment 4

> Username: Pele44  
> Created at: 2024-03-29 14:33:59 UTC  
> Url: https://github.com/boostorg/cobalt/issues/167#issuecomment-2027327383  

ok, just one more question for now. Is this a platform dependent bug? I.e. is it also available on MSVC? Because I haven't noticed any crashes there, but since it's UB, it may be a coincidence...

---

## Comment 5

> Username: Pele44  
> Created at: 2024-03-29 15:49:44 UTC  
> Url: https://github.com/boostorg/cobalt/issues/167#issuecomment-2027409658  

Thanks for the turbo fast response and action! I have already made the changes from b95e42e commit, it seems that they helped and the crash no longer occurs:)

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2024-04-01 13:26:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/167#issuecomment-2029757515  

Fix is in master, will be in the upcoming 1.85 release of boost
