# #214 - ThreadSanitizer support [Closed]

> Username: lenerd  
> Created at: 2019-10-01 23:18:24 UTC  
> Updated at: 2020-04-24 09:52:15 UTC  
> Closed at: 2020-04-24 09:52:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/214  

Hi,  
  
I am currently trying to follow the [documentation](https://www.boost.org/doc/libs/1_71_0/libs/fiber/doc/html/fiber/worker.html) in order to build a pool of worker threads executing fibers.  During development I got warnings from ThreadSanitizer about possible data races.  
  
As I understood the documentation, I can use `boost::fibers::` synchronization primitives like mutexes, condition variables, and futures to synchronize fibers (possibly) running on different threads (as long as `BOOST_FIBERS_NO_ATOMICS` is not defined).  Furthermore, there is an implicit "main" fiber running on each thread.  
  
To start the investigation, I tried to come up with a minimal example that hints at any issue in Boost.fiber or my own code.  I ended up with the following snippet, which is heavily based on the example from the documentation:  
  
```c++  
#include <boost/fiber/all.hpp>  
#include <thread>  
  
int main() {  
  bool term = false;  
  boost::fibers::mutex mtx_term;  
  boost::fibers::condition_variable_any cv_term;  
  // std::mutex mtx_term;  
  // std::condition_variable_any cv_term;  
  
  auto worker = std::thread([&] {  
    {  
      // suspend till signalled  
      std::unique_lock<decltype(mtx_term)> lock(mtx_term);  
      cv_term.wait(lock, [&term] { return term; });  
    }  
  });  
  
  std::this_thread::sleep_for(std::chrono::milliseconds(100));  
  
  // signal termination  
  {  
    std::unique_lock<decltype(mtx_term)> lock(mtx_term);  
    term = true;  
  }  
  cv_term.notify_all();  
  worker.join();  
}  
```  
  
When compiled with  
  
* GCC 9.10,  
* ThreadSanitizer, and  
* Boost 1.71 built with debug symbols and ThreadSanitizer,  
  
TSan complains with the warnings given below during execution.  However, if I use `std::mutex` and `std::condition_variable` instead of their `boost::fibers::` counterparts, TSan does not complain.  
  
It looks like this and other issues which appeared are connected with the implicit instantiations/destructions of Boost.fiber objects like the scheduler.  
  
Are these warnings false positives or a possible source of trouble? Or am I just not using Boost.fiber in the correct way?  
  
Thanks  
  
Lennart  
  
  
---  
  
  
```  
$ ./race_test  
==================  
WARNING: ThreadSanitizer: data race (pid=115107)  
  Write of size 8 at 0x7b2000000048 by thread T1:  
    #0 pthread_cond_destroy /build/gcc/src/gcc/libsanitizer/tsan/tsan_interceptors.cc:1171 (libtsan.so.0+0x2de4b)  
    #1 boost::fibers::algo::round_robin::~round_robin() boost/fiber/algo/round_robin.hpp:33 (libboost_fiber.so.1.71.0+0x11e4a)  
    #2 boost::fibers::algo::round_robin::~round_robin() boost/fiber/algo/round_robin.hpp:33 (libboost_fiber.so.1.71.0+0x11e7e)  
    #3 boost::fibers::algo::intrusive_ptr_release(boost::fibers::algo::algorithm*) boost/fiber/algo/algorithm.hpp:59 (libboost_fiber.so.1.71.0+0x14e47)  
    #4 boost::intrusive_ptr<boost::fibers::algo::algorithm>::~intrusive_ptr() boost/smart_ptr/intrusive_ptr.hpp:98 (libboost_fiber.so.1.71.0+0x268cb)  
    #5 boost::fibers::scheduler::~scheduler() libs/fiber/src/scheduler.cpp:115 (libboost_fiber.so.1.71.0+0x256c4)  
    #6 _M_invoke<0> /usr/include/c++/9.1.0/thread:244 (race_test+0x3e36)  
    #7 operator() /usr/include/c++/9.1.0/thread:251 (race_test+0x3dde)  
    #8 _M_run /usr/include/c++/9.1.0/thread:195 (race_test+0x3d98)  
    #9 execute_native_thread_routine /build/gcc/src/gcc/libstdc++-v3/src/c++11/thread.cc:80 (libstdc++.so.6+0xcecd3)  
  
  Previous read of size 8 at 0x7b2000000048 by main thread:  
    #0 pthread_cond_broadcast /build/gcc/src/gcc/libsanitizer/tsan/tsan_interceptors.cc:1164 (libtsan.so.0+0x2dd28)  
    #1 __gthread_cond_broadcast /build/gcc/src/gcc-build/x86_64-pc-linux-gnu/libstdc++-v3/include/x86_64-pc-linux-gnu/bits/gthr-default.h:853 (libstdc++.so.6+0xc8fed)  
    #2 std::condition_variable::notify_all() /build/gcc/src/gcc/libstdc++-v3/src/c++11/condition_variable.cc:73 (libstdc++.so.6+0xc8fed)  
    #3 boost::fibers::scheduler::schedule_from_remote(boost::fibers::context*) libs/fiber/src/scheduler.cpp:232 (libboost_fiber.so.1.71.0+0x252e4)  
    #4 boost::fibers::context::schedule(boost::fibers::context*) libs/fiber/src/context.cpp:286 (libboost_fiber.so.1.71.0+0x19a8e)  
    #5 boost::fibers::condition_variable_any::notify_all() libs/fiber/src/condition_variable.cpp:56 (libboost_fiber.so.1.71.0+0x19449)  
    #6 main /home/lennart/tmp/fibers/race_test.cpp:26 (race_test+0x36fd)  
    #7 main /home/lennart/tmp/fibers/race_test.cpp:23 (race_test+0x36cf)  
  
  Location is heap block of size 128 at 0x7b2000000000 allocated by thread T1:  
    #0 operator new(unsigned long) /build/gcc/src/gcc/libsanitizer/tsan/tsan_new_delete.cc:63 (libtsan.so.0+0x79c6c)  
    #1 boost::fibers::scheduler::scheduler() libs/fiber/src/scheduler.cpp:112 (libboost_fiber.so.1.71.0+0x26520)  
    #2 boost::fibers::context_initializer::context_initializer() libs/fiber/src/context.cpp:78 (libboost_fiber.so.1.71.0+0x20610)  
    #3 boost::fibers::context::active() libs/fiber/src/context.cpp:106 (libboost_fiber.so.1.71.0+0x1b386)  
    #4 boost::fibers::mutex::lock() libs/fiber/src/mutex.cpp:26 (libboost_fiber.so.1.71.0+0x21e4f)  
    #5 std::unique_lock<boost::fibers::mutex>::lock() /usr/include/c++/9.1.0/bits/unique_lock.h:141 (race_test+0x6891)  
    #6 std::unique_lock<boost::fibers::mutex>::unique_lock(boost::fibers::mutex&) /usr/include/c++/9.1.0/bits/unique_lock.h:71 (race_test+0x58f6)  
    #7 operator() /home/lennart/tmp/fibers/race_test.cpp:14 (race_test+0x3549)  
    #8 __invoke_impl<void, main()::<lambda()> > /usr/include/c++/9.1.0/bits/invoke.h:60 (race_test+0x3f68)  
    #9 __invoke<main()::<lambda()> > /usr/include/c++/9.1.0/bits/invoke.h:95 (race_test+0x3ed3)  
    #10 _M_invoke<0> /usr/include/c++/9.1.0/thread:244 (race_test+0x3e36)  
    #11 operator() /usr/include/c++/9.1.0/thread:251 (race_test+0x3dde)  
    #12 _M_run /usr/include/c++/9.1.0/thread:195 (race_test+0x3d98)  
    #13 execute_native_thread_routine /build/gcc/src/gcc/libstdc++-v3/src/c++11/thread.cc:80 (libstdc++.so.6+0xcecd3)  
  
  Thread T1 (tid=115109, running) created by main thread at:  
    #0 pthread_create /build/gcc/src/gcc/libsanitizer/tsan/tsan_interceptors.cc:964 (libtsan.so.0+0x2d683)  
    #1 __gthread_create /build/gcc/src/gcc-build/x86_64-pc-linux-gnu/libstdc++-v3/include/x86_64-pc-linux-gnu/bits/gthr-default.h:663 (libstdc++.so.6+0xcefa9)  
    #2 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) /build/gcc/src/gcc/libstdc++-v3/src/c++11/thread.cc:135 (libstdc++.so.6+0xcefa9)  
    #3 main /home/lennart/tmp/fibers/race_test.cpp:17 (race_test+0x3690)  
  
SUMMARY: ThreadSanitizer: data race boost/fiber/algo/round_robin.hpp:33 in boost::fibers::algo::round_robin::~round_robin()  
==================  
ThreadSanitizer: reported 1 warnings  
```

---

## Comment 1

> Username: olk  
> Created at: 2019-10-02 06:05:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/214#issuecomment-537350123  

The sanitizers will be confused by the stack switching...  
You have to re-build boost.context and boost.fiber while applying BOOST_USE_ASAN + b2 property context-impl=ucontext in order to get the sanitizers working.  
You could also take a look at https://www.boost.org/doc/libs/1_71_0/libs/fiber/doc/html/fiber/worker.html for fibers + worker-threads.

---

## Comment 2

> Username: lenerd  
> Created at: 2019-10-02 18:20:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/214#issuecomment-537618049  

Thanks a lot for this quick reply and the docs update!  
  
Right now I am trying to figure out if the ThreadSanitizer can be used with fibers, as the `__sanitizer_..._fiber` API seems to be only part of the AddressSanitizer library. I will report back what I find out.

---

## Comment 3

> Username: lenerd  
> Created at: 2019-10-03 16:22:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/214#issuecomment-538018484  

The `__sanitizer_..._fiber` API seems indeed only be part of AddressSanitizer. However, since LLVM 9.0.0 there exists [a separate API for fiber support in ThreadSanitizer](https://github.com/llvm-mirror/compiler-rt/blob/cd9ff1a7763ea4a6996d4fa640640908fffd2db4/include/sanitizer/tsan_interface.h#L139-L155):  
  
```c++  
// Fiber switching API.  
//   - TSAN context for fiber can be created by __tsan_create_fiber  
//     and freed by __tsan_destroy_fiber.  
//   - TSAN context of current fiber or thread can be obtained  
//     by calling __tsan_get_current_fiber.  
//   - __tsan_switch_to_fiber should be called immediatly before switch  
//     to fiber, such as call of swapcontext.  
//   - Fiber name can be set by __tsan_set_fiber_name.  
void *__tsan_get_current_fiber(void);  
void *__tsan_create_fiber(unsigned flags);  
void __tsan_destroy_fiber(void *fiber);  
void __tsan_switch_to_fiber(void *fiber, unsigned flags);  
void __tsan_set_fiber_name(void *fiber, const char *name);  
  
// Flags for __tsan_switch_to_fiber:  
// Do not establish a happens-before relation between fibers  
const unsigned __tsan_switch_to_fiber_no_sync = 1 << 0;  
```  
  
My assumption is that it would be required to integrate this into Boost.Context's [`fiber_ucontext.hpp`](https://github.com/boostorg/context/blob/develop/include/boost/context/fiber_ucontext.hpp) analogous to the ASan integration. So far, my attempts at this have been unsuccessful and the example program just crashes. Maybe the test cases in the [compiler-rt repository](https://github.com/llvm-mirror/compiler-rt/blob/master/test/tsan/fiber_simple.cpp) will provide helpful in this regard.

---

## Comment 4

> Username: olk  
> Created at: 2020-04-24 09:52:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/214#issuecomment-618916798  

belongs to boost.context
