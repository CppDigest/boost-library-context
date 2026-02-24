# #194 - heap-use-after-free error when cancelling spawned awaitable [Open]

> Username: Pele44  
> Created at: 2024-07-22 12:16:34 UTC  
> Updated at: 2025-03-03 09:31:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/194  

In the code that is based on cobalt, crashes occur. When this code is run under address sanitizer, it detects a "heap-use-after-free" error. I managed to prepare a simplified scenario (maybe not the simplest possible) that leads to this memory violation.  
```cpp  
#include <cassert>  
#include <thread>  
  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
  
using namespace boost;  
  
void runContext(asio::io_context& context)  
{  
  cobalt::this_thread::set_executor(context.get_executor());  
  context.run();  
}  
  
class SingleThreadedWithContext  
{  
public:  
  SingleThreadedWithContext()  
    : executorWorkGuard(boost::asio::make_work_guard(ioContext)),  
      contextThread(std::jthread{runContext, std::ref(ioContext)})  
  {  
  }  
  
  ~SingleThreadedWithContext() { executorWorkGuard.reset(); }  
  
  auto getExecutor() { return ioContext.get_executor(); }  
  
private:  
  asio::io_context ioContext;  
  asio::executor_work_guard<decltype(ioContext)::executor_type> executorWorkGuard;  
  std::jthread contextThread;  
};  
  
cobalt::task<void> emptyTask()  
{  
  co_return;  
}  
  
cobalt::promise<void> promisThatSpawnsEmptyTaskToAnotherThread(auto executorToSpawn)  
{  
  co_await cobalt::spawn(executorToSpawn, emptyTask(), cobalt::use_op);  
}  
  
cobalt::task<void> makePromiseThatSpawnsToAnotherThreadAndCancelIt(auto executorToSpawn)  
{  
  auto prom = promisThatSpawnsEmptyTaskToAnotherThread(executorToSpawn);  
  assert(prom);  
  prom.cancel();  
  co_await prom;  
}  
  
int main(int /*argc*/, const char** /*argv*/)  
{  
  SingleThreadedWithContext threadA;  
  SingleThreadedWithContext threadB;  
  
  cobalt::spawn(threadA.getExecutor(),  
                makePromiseThatSpawnsToAnotherThreadAndCancelIt(threadB.getExecutor()),  
                asio::use_future)  
    .get();  
  
  return 0;  
}  
```  
  
My analysis led me to the conclusion that when we call the spawn method, one of the variables (`recs`) is allocated as a shared_ptr, but on a memory block that is probably saved on the coroutine frame (cobalt/include/boost/cobalt/detail/spawn.hpp:111 -> `auto recs = std::allocate_shared<detail::task_receiver<void>>(alloc, std::move(a.receiver_));`)  
When the coroutine ends, the frame is freed, along with the memory pointed to by the aforementioned shared_ptr. The problem is that access to this shared_ptr is done by another thread and can occur after the frame has been removed. From the point of view of shared_ptr, everything seems to be ok, because the reference count is positive, but because we allocated the object itself (with the counter) on a "local" piece of memory, the whole thing can lead to access to the freed memory.  
  
```cpp  
    if (sl.is_connected())  
      sl.assign(  
          [ex = exec, recs](asio::cancellation_type ct)  
          {  
            asio::dispatch(ex, [recs, ct] {recs->cancel(ct); // crash here -> recs point to freed memory });  
          });  
```  
  
e.g. changing allocation to `    auto recs = std::make_shared<detail::task_receiver<void>>(std::move(a.receiver_));`  resolves a problem  
  
gcc 14.1, example build with address sanitizer

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-07-22 13:19:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/194#issuecomment-2242944586  

Are you on HEAD or the boost 1.84 release version? Because I can't reproduce the error above, but had a similar looking one before 61bf8d46.

---

## Comment 2

> Username: Pele44  
> Created at: 2024-07-22 13:28:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/194#issuecomment-2242964144  

I'm working on boost 1.85, which already has the mentioned changes (https://github.com/boostorg/cobalt/commit/61bf8d467786fefe8a8ab6f7cb51896bcade6d46)

---

## Comment 3

> Username: Pele44  
> Created at: 2024-07-22 13:30:22 UTC  
> Updated at: 2024-07-22 13:31:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/194#issuecomment-2242968496  

The error is not 100% reproducible, but in my environment, at least once in 10 launches the error occurred. I tested last time on debug, without optimization. Fedora 40, AMD Ryzen 7 5825U

---

## Comment 4

> Username: Pele44  
> Created at: 2024-07-23 09:14:12 UTC  
> Updated at: 2024-07-23 09:14:41 UTC  
> Url: https://github.com/boostorg/cobalt/issues/194#issuecomment-2244681179  

```  
=================================================================  
==19516==ERROR: AddressSanitizer: heap-use-after-free on address 0x5210000001d8 at pc 0x000000438afa bp 0x7f560d1ff4c0 sp 0x7f560d1ff4b8  
READ of size 1 at 0x5210000001d8 thread T2  
    #0 0x438af9 in boost::cobalt::detail::task_receiver<void>::cancel(boost::asio::cancellation_type) const (arch_cmd+0x438af9) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #1 0x4543b5 in boost::cobalt::detail::async_initiate_spawn::operator()<boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr> >(boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr>&&, boost::cobalt::task<void>, boost::asio::any_io_executor)::{lambda(boost::asio::cancellation_type)#1}::operator()(boost::asio::cancellation_type) const::{lambda()#1}::operator()() const (arch_cmd+0x4543b5) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #2 0x45af11 in boost::asio::detail::binder0<boost::cobalt::detail::async_initiate_spawn::operator()<boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr> >(boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr>&&, boost::cobalt::task<void>, boost::asio::any_io_executor)::{lambda(boost::asio::cancellation_type)#1}::operator()(boost::asio::cancellation_type) const::{lambda()#1}>::operator()() (arch_cmd+0x45af11) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #3 0x45b3a5 in void boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<boost::cobalt::detail::async_initiate_spawn::operator()<boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr> >(boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr>&&, boost::cobalt::task<void>, boost::asio::any_io_executor)::{lambda(boost::asio::cancellation_type)#1}::operator()(boost::asio::cancellation_type) const::{lambda()#1}>, std::allocator<void> >(boost::asio::detail::executor_function::impl_base*, bool) (arch_cmd+0x45b3a5) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #4 0x40f958 in boost::asio::detail::executor_function::operator()() (arch_cmd+0x40f958) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #5 0x44661d in boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) (arch_cmd+0x44661d) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #6 0x415f26 in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) (arch_cmd+0x415f26) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #7 0x41cb64 in boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) (arch_cmd+0x41cb64) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #8 0x41c0df in boost::asio::detail::scheduler::run(boost::system::error_code&) (arch_cmd+0x41c0df) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #9 0x41e27d in boost::asio::io_context::run() (arch_cmd+0x41e27d) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #10 0x405a54 in runContext(boost::asio::io_context&) main.cpp:12  
    #11 0x456fde in void std::__invoke_impl<void, void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >(std::__invoke_other, void (*&&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x456fde) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #12 0x456630 in std::__invoke_result<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >::type std::__invoke<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >(void (*&&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x456630) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #13 0x455894 in void std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > >::_M_invoke<0ul, 1ul>(std::_Index_tuple<0ul, 1ul>) (arch_cmd+0x455894) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #14 0x453f97 in std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > >::operator()() (arch_cmd+0x453f97) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #15 0x451fa9 in std::thread::_State_impl<std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > > >::_M_run() (arch_cmd+0x451fa9) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #16 0x7f56110e7563 in execute_native_thread_routine (/lib64/libstdc++.so.6+0xe7563) (BuildId: 53d7dd48d119bbf0b4d715f2d113abda0896a8d0)  
    #17 0x7f561145df95 in asan_thread_start(void*) (/lib64/libasan.so.8+0x5df95) (BuildId: c1431025b5d8af781c22c9ceea71f065c547d32d)  
    #18 0x7f5610ea6506 in start_thread (/lib64/libc.so.6+0x97506) (BuildId: 8f53abaad945a669f2bdcd25f471d80e077568ef)  
    #19 0x7f5610f2a40b in clone3 (/lib64/libc.so.6+0x11b40b) (BuildId: 8f53abaad945a669f2bdcd25f471d80e077568ef)  
  
0x5210000001d8 is located 216 bytes inside of 4488-byte region [0x521000000100,0x521000001288)  
freed by thread T1 here:  
    #0 0x7f56114f9ae5 in operator delete(void*, unsigned long, std::align_val_t) (/lib64/libasan.so.8+0xf9ae5) (BuildId: c1431025b5d8af781c22c9ceea71f065c547d32d)  
    #1 0x40c6a8 in std::pmr::memory_resource::deallocate(void*, unsigned long, unsigned long) /usr/include/c++/14/bits/memory_resource.h:81  
    #2 0x4203f5 in boost::cobalt::promise_memory_resource_base::operator delete(void*, unsigned long) (arch_cmd+0x4203f5) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #3 0x40a456 in promisThatSpawnsEmptyTaskToAnotherThread main.cpp:42  
    #4 0x40a6c1 in promisThatSpawnsEmptyTaskToAnotherThread main.cpp:39  
    #5 0x440117 in std::__n4861::coroutine_handle<boost::cobalt::detail::cobalt_promise<void> >::destroy() const (arch_cmd+0x440117) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #6 0x43c6db in void boost::cobalt::detail::self_destroy<boost::cobalt::detail::cobalt_promise<void> >(std::__n4861::coroutine_handle<boost::cobalt::detail::cobalt_promise<void> >) (arch_cmd+0x43c6db) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #7 0x437a32 in boost::cobalt::detail::cobalt_promise<void>::final_awaitable::await_suspend(std::__n4861::coroutine_handle<boost::cobalt::detail::cobalt_promise<void> >) (arch_cmd+0x437a32) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #8 0x40a32e in promisThatSpawnsEmptyTaskToAnotherThread main.cpp:42  
    #9 0x41dbe5 in std::__n4861::coroutine_handle<void>::resume() const (arch_cmd+0x41dbe5) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #10 0x41db85 in std::__n4861::coroutine_handle<void>::operator()() const (arch_cmd+0x41db85) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #11 0x455e9a in boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr>::operator()(std::__exception_ptr::exception_ptr) (arch_cmd+0x455e9a) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #12 0x454522 in boost::cobalt::detail::async_initiate_spawn::operator()<boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr> >(boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr>&&, boost::cobalt::task<void>, boost::asio::any_io_executor)::completion_handler::operator()() (arch_cmd+0x454522) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #13 0x45bb4d in boost::asio::detail::binder0<boost::cobalt::detail::async_initiate_spawn::operator()<boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr> >(boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr>&&, boost::cobalt::task<void>, boost::asio::any_io_executor)::completion_handler>::operator()() (arch_cmd+0x45bb4d) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #14 0x45bff0 in void boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<boost::cobalt::detail::async_initiate_spawn::operator()<boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr> >(boost::cobalt::completion_handler<std::__exception_ptr::exception_ptr>&&, boost::cobalt::task<void>, boost::asio::any_io_executor)::completion_handler>, std::allocator<void> >(boost::asio::detail::executor_function::impl_base*, bool) (arch_cmd+0x45bff0) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #15 0x40f958 in boost::asio::detail::executor_function::operator()() (arch_cmd+0x40f958) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #16 0x44661d in boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) (arch_cmd+0x44661d) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #17 0x415f26 in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) (arch_cmd+0x415f26) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #18 0x41cb64 in boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) (arch_cmd+0x41cb64) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #19 0x41c0df in boost::asio::detail::scheduler::run(boost::system::error_code&) (arch_cmd+0x41c0df) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #20 0x41e27d in boost::asio::io_context::run() (arch_cmd+0x41e27d) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #21 0x405a54 in runContext(boost::asio::io_context&) main.cpp:12  
    #22 0x456fde in void std::__invoke_impl<void, void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >(std::__invoke_other, void (*&&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x456fde) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #23 0x456630 in std::__invoke_result<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >::type std::__invoke<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >(void (*&&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x456630) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #24 0x455894 in void std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > >::_M_invoke<0ul, 1ul>(std::_Index_tuple<0ul, 1ul>) (arch_cmd+0x455894) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #25 0x453f97 in std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > >::operator()() (arch_cmd+0x453f97) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #26 0x451fa9 in std::thread::_State_impl<std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > > >::_M_run() (arch_cmd+0x451fa9) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #27 0x7f56110e7563 in execute_native_thread_routine (/lib64/libstdc++.so.6+0xe7563) (BuildId: 53d7dd48d119bbf0b4d715f2d113abda0896a8d0)  
  
previously allocated by thread T1 here:  
    #0 0x7f56114f89e8 in operator new(unsigned long, std::align_val_t) (/lib64/libasan.so.8+0xf89e8) (BuildId: c1431025b5d8af781c22c9ceea71f065c547d32d)  
    #1 0x40c617 in std::pmr::memory_resource::allocate(unsigned long, unsigned long) /usr/include/c++/14/bits/memory_resource.h:76  
    #2 0x431684 in void* boost::cobalt::promise_memory_resource_base::operator new<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul> >(unsigned long, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul>&) (arch_cmd+0x431684) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #3 0x430b40 in boost::cobalt::promise<void> promisThatSpawnsEmptyTaskToAnotherThread<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul> >(boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul>) (arch_cmd+0x430b40) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #4 0x4069d0 in makePromiseThatSpawnsToAnotherThreadAndCancelIt main.cpp:46  
    #5 0x44ec81 in std::__n4861::coroutine_handle<boost::cobalt::detail::task_promise<void> >::resume() const (arch_cmd+0x44ec81) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #6 0x44e0f3 in std::__n4861::coroutine_handle<boost::cobalt::detail::task_promise<void> >::operator()() const (arch_cmd+0x44e0f3) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #7 0x44d14f in boost::asio::detail::binder0<std::__n4861::coroutine_handle<boost::cobalt::detail::task_promise<void> > >::operator()() (arch_cmd+0x44d14f) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #8 0x44d575 in void boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<std::__n4861::coroutine_handle<boost::cobalt::detail::task_promise<void> > >, std::allocator<void> >(boost::asio::detail::executor_function::impl_base*, bool) (arch_cmd+0x44d575) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #9 0x40f958 in boost::asio::detail::executor_function::operator()() (arch_cmd+0x40f958) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #10 0x44661d in boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) (arch_cmd+0x44661d) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #11 0x415f26 in boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) (arch_cmd+0x415f26) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #12 0x41cb64 in boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) (arch_cmd+0x41cb64) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #13 0x41c0df in boost::asio::detail::scheduler::run(boost::system::error_code&) (arch_cmd+0x41c0df) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #14 0x41e27d in boost::asio::io_context::run() (arch_cmd+0x41e27d) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #15 0x405a54 in runContext(boost::asio::io_context&) main.cpp:12  
    #16 0x456fde in void std::__invoke_impl<void, void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >(std::__invoke_other, void (*&&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x456fde) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #17 0x456630 in std::__invoke_result<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >::type std::__invoke<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >(void (*&&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x456630) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #18 0x455894 in void std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > >::_M_invoke<0ul, 1ul>(std::_Index_tuple<0ul, 1ul>) (arch_cmd+0x455894) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #19 0x453f97 in std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > >::operator()() (arch_cmd+0x453f97) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #20 0x451fa9 in std::thread::_State_impl<std::thread::_Invoker<std::tuple<void (*)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> > > >::_M_run() (arch_cmd+0x451fa9) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #21 0x7f56110e7563 in execute_native_thread_routine (/lib64/libstdc++.so.6+0xe7563) (BuildId: 53d7dd48d119bbf0b4d715f2d113abda0896a8d0)  
  
Thread T2 created by T0 here:  
    #0 0x7f56114ef871 in pthread_create (/lib64/libasan.so.8+0xef871) (BuildId: c1431025b5d8af781c22c9ceea71f065c547d32d)  
    #1 0x7f56110e7638 in std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) (/lib64/libstdc++.so.6+0xe7638) (BuildId: 53d7dd48d119bbf0b4d715f2d113abda0896a8d0)  
    #2 0x430034 in std::thread std::jthread::_S_create<void (&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >(std::stop_source&, void (&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x430034) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #3 0x42a709 in std::jthread::jthread<void (&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>, void>(void (&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x42a709) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #4 0x421aee in SingleThreadedWithContext::SingleThreadedWithContext() (arch_cmd+0x421aee) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #5 0x406353 in main main.cpp:55  
    #6 0x7f5610e39087 in __libc_start_call_main (/lib64/libc.so.6+0x2a087) (BuildId: 8f53abaad945a669f2bdcd25f471d80e077568ef)  
    #7 0x7f5610e3914a in __libc_start_main_alias_2 (/lib64/libc.so.6+0x2a14a) (BuildId: 8f53abaad945a669f2bdcd25f471d80e077568ef)  
    #8 0x405864 in _start (arch_cmd+0x405864) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
  
Thread T1 created by T0 here:  
    #0 0x7f56114ef871 in pthread_create (/lib64/libasan.so.8+0xef871) (BuildId: c1431025b5d8af781c22c9ceea71f065c547d32d)  
    #1 0x7f56110e7638 in std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) (/lib64/libstdc++.so.6+0xe7638) (BuildId: 53d7dd48d119bbf0b4d715f2d113abda0896a8d0)  
    #2 0x430034 in std::thread std::jthread::_S_create<void (&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context> >(std::stop_source&, void (&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x430034) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #3 0x42a709 in std::jthread::jthread<void (&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>, void>(void (&)(boost::asio::io_context&), std::reference_wrapper<boost::asio::io_context>&&) (arch_cmd+0x42a709) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #4 0x421aee in SingleThreadedWithContext::SingleThreadedWithContext() (arch_cmd+0x421aee) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
    #5 0x406347 in main main.cpp:54  
    #6 0x7f5610e39087 in __libc_start_call_main (/lib64/libc.so.6+0x2a087) (BuildId: 8f53abaad945a669f2bdcd25f471d80e077568ef)  
    #7 0x7f5610e3914a in __libc_start_main_alias_2 (/lib64/libc.so.6+0x2a14a) (BuildId: 8f53abaad945a669f2bdcd25f471d80e077568ef)  
    #8 0x405864 in _start (arch_cmd+0x405864) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee)  
  
SUMMARY: AddressSanitizer: heap-use-after-free (arch_cmd+0x438af9) (BuildId: bf889d36de56770076a17b7631727d2b663eaaee) in boost::cobalt::detail::task_receiver<void>::cancel(boost::asio::cancellation_type) const  
Shadow bytes around the buggy address:  
  0x520fffffff00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x520fffffff80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x521000000000: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x521000000080: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x521000000100: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
=>0x521000000180: fd fd fd fd fd fd fd fd fd fd fd[fd]fd fd fd fd  
  0x521000000200: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x521000000280: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x521000000300: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x521000000380: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
  0x521000000400: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
==19516==ABORTING  
```

---

## Comment 5

> Username: Pele44  
> Created at: 2024-08-12 13:11:35 UTC  
> Url: https://github.com/boostorg/cobalt/issues/194#issuecomment-2283947598  

I've added https://github.com/boostorg/cobalt/pull/196

---

## Comment 6

> Username: Pele44  
> Created at: 2025-03-03 09:31:19 UTC  
> Url: https://github.com/boostorg/cobalt/issues/194#issuecomment-2693758608  

I've been trying to open this bug on my current machine for a few hours. But boost versions 1.85 and 1.86 didn't cause any problems. I've tried address sanitizer, thread sanitizer, gcc 14.2 and clang 19.1.7. All on Fedora 41 with the latest updates.  
It seems that updating gcc to 14.2 caused the bug to stop reproducing. There were crashes on 13.2 and 14.1. Due to a lot of confusion and lack of time, I don't want to go back to these older versions of gcc. However, the behavior of the example program was not deterministic before, so I cannot say for sure whether the problem is not likely to occur in the future. I will monitor this, because we have tests connected to CI that crashed because of this accident. At the moment we are using our fix, but as soon as boost 1.88 appears, we have a plan to update. I think that at this point the task can be considered frozen/closed.
