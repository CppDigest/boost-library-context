# #2615 - Data race when closing websocket when using multiple threads for io_context [Closed]

> Username: Oipo  
> Created at: 2023-01-05 22:06:31 UTC  
> Updated at: 2023-10-28 16:45:39 UTC  
> Closed at: 2023-10-28 16:45:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2615  

### Version of Beast  
  
330  
  
### Steps necessary to reproduce the problem  
The code is available on github, but it's a lot more than just boost BEAST due to combining separate event loops. It distills down to this:  
```  
    // create 4 threads for the io_context to run on  
    auto context = std::make_unique<net::io_context>();  
    std::vector<std::thread> threads{};  
    for(uint64_t i = 0; i < 4; i++) {  
        threads.emplace_back([]() {  
            while (!context->stopped()) {  
                try {  
                    context->run();  
                } catch (boost::system::system_error const &e) {  
                    ... logging omitted for brevity ...  
                }  
            }  
        });  
    }  
  
    // either create a ws connection or get one from a host and start the read loop  
    ... omitted for brevity, assume that the _ws member variable has been set somewhere ...  
    ... type is std::shared_ptr<websocket::stream<beast::tcp_stream>> ...  
      
    // spawn a read loop  
    net::spawn(*context, [](net::yield_context yield) {  
        beast::error_code ec;  
  
        while(true) {  
            beast::basic_flat_buffer buffer{std::allocator<uint8_t>{}};  
  
            _ws->async_read(buffer, yield[ec]);  
  
            ... error checking and sending data to different event loop omitted for brevity ...  
        }  
    }  
  
    // at some point, close the connection when we're done with it  
    net::spawn(*context, [](net::yield_context yield) {  
        boost::system::error_code ec;  
        _ws->async_close(beast::websocket::close_code::normal, yield[ec]);  
        if (ec) {  
            ...  
        }  
    });  
```  
  
My first thought is that the `async_close` has to be executed on the same thread as the thread running the `async_read` loop, but I don't know how to do that other than keep spawning new functions to run until we hit the same one. Do you know of a better way?  
  
### All relevant compiler information  
Given threadsanitizer trace is from program compiled with clang++ version 16.0.0 (++20230103042323+3bbdd9f506c1-1~exp1~20230103042429.660), but a similar trace also appears when using gcc 11.3.0  
  
Thread sanitizer warning:  
[tsan data race.txt](https://github.com/boostorg/beast/files/10355959/tsan.data.race.txt)  
  
  
(ignore any reference to `Http`, working on refactoring and haven't gotten around to renaming)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-01-06 22:25:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374204567  

Use the overload of `spawn` that accepts an executor, and make sure that all of the websocket coroutines use the same `strand`.

---

## Comment 2

> Username: Oipo  
> Created at: 2023-01-08 14:10:25 UTC  
> Updated at: 2023-01-08 14:12:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374845838  

Ah, yes, that solved that one. Thanks :)  
  
I'm running into the following segmentation fault running threadsaniziter, but I don't have a clue where this one is coming from. This one also only happens when using >1 `io_context` threads. I'm aware that there is a [threadsanitizer missing feature which leads to segfaults with boost 1.80+](https://stackoverflow.com/a/73375092), but I'm specifically running 1.79.0. Is there anything I can do to zoom in on where it may be coming from?  
  
GCC:  
```  
Thread 6 "Asio #3-3" received signal SIGSEGV, Segmentation fault.  
[Switching to Thread 0x7ffff19ff640 (LWP 880028)]  
0x00007ffff749ee34 in __tsan::FuncEntry (thr=thr@entry=0x7ffff19bd2c0, pc=pc@entry=5364834) at ../../../../src/libsanitizer/tsan/tsan_rtl.cpp:1039  
1039	../../../../src/libsanitizer/tsan/tsan_rtl.cpp: No such file or directory.  
(gdb) bt  
#0  0x00007ffff749ee34 in __tsan::FuncEntry (thr=thr@entry=0x7ffff19bd2c0, pc=pc@entry=5364834) at ../../../../src/libsanitizer/tsan/tsan_rtl.cpp:1039  
#1  0x00007ffff74539d3 in __tsan::ScopedInterceptor::ScopedInterceptor (fname=0x7ffff74ddbc4 "pthread_mutex_lock", pc=5364834, thr=<optimized out>, this=0x7ffff19bc780) at ../../../../src/libsanitizer/tsan/tsan_interceptors_posix.cpp:257  
#2  __interceptor_pthread_mutex_lock (m=0x7b4000000038) at ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:4240  
#3  0x000000000051dc62 in boost::asio::detail::posix_mutex::lock (this=0x7b4000000038) at /usr/include/boost/asio/detail/posix_mutex.hpp:52  
#4  boost::asio::detail::conditionally_enabled_mutex::scoped_lock::lock (this=0x7ffff19bc7d0) at /usr/include/boost/asio/detail/conditionally_enabled_mutex.hpp:74  
#5  boost::asio::detail::scheduler::run (this=0x7b4000000000, ec=...) at /usr/include/boost/asio/detail/impl/scheduler.ipp:210  
#6  0x0000000000505c0f in boost::asio::io_context::run (this=0x7b0400000610) at /usr/include/boost/asio/impl/io_context.ipp:63  
#7  operator() (__closure=__closure@entry=0x7b0800000aa8) at /home/oipo/Programming/Ichor/src/services/network/AsioContextService.cpp:69  
#8  0x0000000000506130 in std::__invoke_impl<void, Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> > (__f=...) at /usr/include/c++/11/bits/invoke.h:60  
#9  std::__invoke<Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> > (__fn=...) at /usr/include/c++/11/bits/invoke.h:96  
#10 std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> > >::_M_invoke<0> (this=0x7b0800000aa8) at /usr/include/c++/11/bits/std_thread.h:253  
#11 std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> > >::operator() (this=0x7b0800000aa8) at /usr/include/c++/11/bits/std_thread.h:260  
#12 std::thread::_State_impl<std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> > > >::_M_run(void) (this=0x7b0800000aa0) at /usr/include/c++/11/bits/std_thread.h:211  
#13 0x00007ffff70dc2b3 in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#14 0x00007ffff742e600 in __tsan_thread_start_func (arg=0x7fffffffcb30) at ../../../../src/libsanitizer/tsan/tsan_interceptors_posix.cpp:959  
#15 0x00007ffff6c94b43 in start_thread (arg=<optimized out>) at ./nptl/pthread_create.c:442  
#16 0x00007ffff6d26a00 in clone3 () at ../sysdeps/unix/sysv/linux/x86_64/clone3.S:81  
  
```  
  
Clang:  
```  
Thread 5 "Asio #3-2" received signal SIGSEGV, Segmentation fault.  
[Switching to Thread 0x7ffff23fc640 (LWP 878798)]  
0x0000000000440d20 in __sanitizer::StackDepotBase<__sanitizer::StackDepotNode, 1, 20>::Put(__sanitizer::StackTrace, bool*) ()  
(gdb) bt  
#0  0x0000000000440d20 in __sanitizer::StackDepotBase<__sanitizer::StackDepotNode, 1, 20>::Put(__sanitizer::StackTrace, bool*) ()  
#1  0x00000000004c0e0c in __tsan::CurrentStackId(__tsan::ThreadState*, unsigned long) ()  
#2  0x00000000004288c3 in __sanitizer::DD::MutexInit(__sanitizer::DDCallback*, __sanitizer::DDMutex*) ()  
#3  0x00000000004cc4cd in __tsan::DDMutexInit(__tsan::ThreadState*, unsigned long, __tsan::SyncVar*) ()  
#4  0x00000000004d7653 in __tsan::MetaMap::GetSync(__tsan::ThreadState*, unsigned long, unsigned long, bool, bool) ()  
#5  0x00000000004aac77 in __tsan_atomic32_fetch_add ()  
#6  0x00000000005e1492 in __gnu_cxx::__exchange_and_add (__mem=0x7b08000009ec, __val=-1) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/ext/atomicity.h:66  
#7  __gnu_cxx::__exchange_and_add_dispatch (__mem=0x7b08000009ec, __val=-1) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/ext/atomicity.h:101  
#8  std::_Sp_counted_base<(__gnu_cxx::_Lock_policy)2>::_M_release_last_use (this=0x7b08000009e0) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/shared_ptr_base.h:187  
#9  std::_Sp_counted_base<(__gnu_cxx::_Lock_policy)2>::_M_release (this=0x7b08000009e0) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/shared_ptr_base.h:361  
#10 std::__shared_count<(__gnu_cxx::_Lock_policy)2>::~__shared_count (this=<optimized out>) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/shared_ptr_base.h:1071  
#11 std::__shared_ptr<boost::asio::detail::strand_executor_service::strand_impl, (__gnu_cxx::_Lock_policy)2>::~__shared_ptr (this=0x7ffff23f8a50) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/shared_ptr_base.h:1524  
#12 boost::asio::strand<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> >::~strand (this=0x7ffff23f8a48) at /usr/include/boost/asio/strand.hpp:151  
#13 boost::asio::execution::detail::any_executor_base::destroy_object<boost::asio::strand<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> > > (ex=...) at /usr/include/boost/asio/execution/any_executor.hpp:766  
#14 0x00000000005d9f68 in boost::asio::execution::detail::any_executor_base::~any_executor_base (this=<optimized out>) at /usr/include/boost/asio/execution/any_executor.hpp:536  
#15 boost::asio::detail::handler_work_base<boost::asio::any_io_executor, boost::asio::any_io_executor, boost::asio::io_context, boost::asio::executor, void>::~handler_work_base (this=<optimized out>) at /usr/include/boost/asio/detail/handler_work.hpp:63  
#16 boost::asio::detail::handler_work<boost::asio::detail::coro_handler<boost::asio::executor_binder<void (*)(), boost::asio::any_io_executor>, void>, boost::asio::any_io_executor, void>::~handler_work (this=0x7ffff23f8a10) at /usr/include/boost/asio/detail/handler_work.hpp:451  
#17 boost::asio::detail::wait_handler<boost::asio::detail::coro_handler<boost::asio::executor_binder<void (*)(), boost::asio::any_io_executor>, void>, boost::asio::any_io_executor>::do_complete (owner=0x7b4000000000, base=<optimized out>) at /usr/include/boost/asio/detail/wait_handler.hpp:79  
#18 0x00000000005c941a in boost::asio::detail::scheduler_operation::complete (this=0x7b440000ff00, owner=0x7b4000000000, ec=..., bytes_transferred=0) at /usr/include/boost/asio/detail/scheduler_operation.hpp:40  
#19 boost::asio::detail::scheduler::do_run_one (this=0x7b4000000000, lock=..., this_thread=..., ec=...) at /usr/include/boost/asio/detail/impl/scheduler.ipp:492  
#20 0x00000000005c8f35 in boost::asio::detail::scheduler::run (this=0x7b4000000000, ec=...) at /usr/include/boost/asio/detail/impl/scheduler.ipp:210  
#21 0x00000000005c8be0 in boost::asio::io_context::run (this=0x7b0400000610) at /usr/include/boost/asio/impl/io_context.ipp:63  
#22 0x00000000005c15f8 in Ichor::AsioContextService::start()::$_0::operator()() const (this=<optimized out>) at /home/oipo/Programming/Ichor/src/services/network/AsioContextService.cpp:69  
#23 std::__invoke_impl<void, Ichor::AsioContextService::start()::$_0>(std::__invoke_other, Ichor::AsioContextService::start()::$_0&&) (__f=...) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/invoke.h:61  
#24 std::__invoke<Ichor::AsioContextService::start()::$_0>(Ichor::AsioContextService::start()::$_0&&) (__fn=...) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/invoke.h:96  
#25 std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start()::$_0> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) (this=<optimized out>) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/std_thread.h:252  
#26 std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start()::$_0> >::operator()() (this=<optimized out>) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/std_thread.h:259  
#27 std::thread::_State_impl<std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start()::$_0> > >::_M_run() (this=<optimized out>) at /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/bits/std_thread.h:210  
#28 0x00007ffff7cdc2b3 in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#29 0x00000000004536ff in __tsan_thread_start_func ()  
#30 0x00007ffff7894b43 in start_thread (arg=<optimized out>) at ./nptl/pthread_create.c:442  
#31 0x00007ffff7926a00 in clone3 () at ../sysdeps/unix/sysv/linux/x86_64/clone3.S:81  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-01-08 14:12:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374846352  

Thread sanitizer can get a little confused when stackful coroutines yield, did you add an exception to the configuration? https://github.com/google/sanitizers/issues/1150

---

## Comment 4

> Username: Oipo  
> Created at: 2023-01-08 14:25:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374848941  

Unfortunately, adding `called_from_lib:libunwind.so` to the suppressions file does not seem to help. That linked issue is still open to this day and mentions that while there is a tsan fibers API, [it would need to be added manually to boost](https://github.com/google/sanitizers/issues/1150#issuecomment-540494078).   
  
Given that running with asan/ubsan does not seem to give any segfaults, perhaps that suggests that it is indeed some tsan/fibers related issue?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-01-08 14:26:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374849290  

@klemens-morgenstern didn't we have a similar issue recently?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-01-08 15:12:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374858910  

We did, but I think this is a different issue, namely that the old `spawn` api is broken. I think you should try to add a completion token to the `spawn` call and see if that's the issue.  
  
Once you've done that everything will run on `boost.context` and you can see if that'll work for you:  
  
https://www.boost.org/doc/libs/master/libs/context/doc/html/context/stack/sanitizers.html

---

## Comment 7

> Username: Oipo  
> Created at: 2023-01-08 20:31:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374920985  

As far as I can see in boost 1.79.0, `spawn` does not include an overload supporting completion tokens. It seems this was introduced in 1.80?

---

## Comment 8

> Username: Oipo  
> Created at: 2023-01-08 20:59:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374925884  

Compiled boost 1.81.0 with   
```  
sudo ./b2 --prefix=/usr variant=release threading=multi link=static context-impl=ucontext cxxflags="-fsanitize=thread -fno-omit-frame-pointer" cflags="-fsanitize=thread -fno-omit-frame-pointer" linkflags="-fsanitize=thread" install  
```  
  
Compiled my code with  
```  
-DBOOST_USE_UCONTEXT -DBOOST_ASIO_DISABLE_BOOST_COROUTINE -fsanitize=thread -fno-omit-frame-pointer ...  
```  
  
Changed all `net::spawn` to  
```  
net::spawn(*_context, [this](net::yield_context yield) {  
    ...  
}, [](std::exception_ptr e) { if (e) std::rethrow_exception(e); });  
```  
  
Got a different stacktrace, but still something similar:  
```  
Thread 3 "Asio #3-0" received signal SIGSEGV, Segmentation fault.  
[Switching to Thread 0x7ffff33fe640 (LWP 950838)]  
0x00007ffff749d880 in __tsan::FuncEntry (pc=5678882, thr=0x7ffff33bc280) at ../../../../src/libsanitizer/tsan/tsan_rtl.cpp:1039  
1039	../../../../src/libsanitizer/tsan/tsan_rtl.cpp: No such file or directory.  
(gdb) bt  
#0  0x00007ffff749d880 in __tsan::FuncEntry (pc=5678882, thr=0x7ffff33bc280) at ../../../../src/libsanitizer/tsan/tsan_rtl.cpp:1039  
#1  __tsan_func_entry (pc=0x56a722 <boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)+233>) at ../../../../src/libsanitizer/tsan/tsan_interface_inl.h:104  
#2  0x000000000054cf0e in boost::asio::detail::std_fenced_block::~std_fenced_block (this=0x7ffff33bb3bf, __in_chrg=<optimized out>) at /usr/include/boost/asio/detail/std_fenced_block.hpp:50  
#3  0x000000000056a722 in boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete (owner=0x7b14000000f0, base=0x7b1000000300) at /usr/include/boost/asio/detail/executor_op.hpp:72  
#4  0x000000000054cf8b in boost::asio::detail::scheduler_operation::complete (this=0x7b1000000300, owner=0x7b14000000f0, ec=..., bytes_transferred=0) at /usr/include/boost/asio/detail/scheduler_operation.hpp:40  
#5  0x00000000005b56bb in boost::asio::detail::strand_executor_service::run_ready_handlers (impl=std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> (use count 1, weak count 0) = {...}) at /usr/include/boost/asio/detail/impl/strand_executor_service.ipp:150  
#6  0x00000000006fd3bc in boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const, void>::operator() (this=0x7ffff33bb590) at /usr/include/boost/asio/detail/impl/strand_executor_service.hpp:136  
#7  0x00000000006e5719 in boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const, void>, boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const, void> > (  
    function=..., context=...) at /usr/include/boost/asio/detail/handler_invoke_helpers.hpp:51  
#8  0x000000000072baba in boost::asio::detail::executor_op<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4ul> const, void>, boost::asio::detail::recycling_allocator<void, boost::asio::detail::thread_info_base::default_tag>, boost::asio::detail::scheduler_operation>::do_complete (  
    owner=0x7b4000000000, base=0x7b580003ff00) at /usr/include/boost/asio/detail/executor_op.hpp:70  
#9  0x000000000054cf8b in boost::asio::detail::scheduler_operation::complete (this=0x7b580003ff00, owner=0x7b4000000000, ec=..., bytes_transferred=0) at /usr/include/boost/asio/detail/scheduler_operation.hpp:40  
#10 0x00000000005526e1 in boost::asio::detail::scheduler::do_run_one (this=0x7b4000000000, lock=..., this_thread=..., ec=...) at /usr/include/boost/asio/detail/impl/scheduler.ipp:492  
#11 0x0000000000551f82 in boost::asio::detail::scheduler::run (this=0x7b4000000000, ec=...) at /usr/include/boost/asio/detail/impl/scheduler.ipp:210  
#12 0x0000000000552f9e in boost::asio::io_context::run (this=0x7b0400000610) at /usr/include/boost/asio/impl/io_context.ipp:63  
#13 0x0000000000543a09 in operator() (__closure=0x7b08000009c8) at /home/oipo/Programming/Ichor/src/services/network/AsioContextService.cpp:69  
#14 0x000000000054988f in std::__invoke_impl<void, Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> >(std::__invoke_other, struct {...} &&) (__f=...) at /usr/include/c++/11/bits/invoke.h:61  
#15 0x0000000000549806 in std::__invoke<Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> >(struct {...} &&) (__fn=...) at /usr/include/c++/11/bits/invoke.h:96  
#16 0x0000000000549767 in std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> > >::_M_invoke<0>(std::_Index_tuple<0>) (this=0x7b08000009c8) at /usr/include/c++/11/bits/std_thread.h:253  
#17 0x00000000005496ff in std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> > >::operator()(void) (this=0x7b08000009c8) at /usr/include/c++/11/bits/std_thread.h:260  
#18 0x0000000000548e3d in std::thread::_State_impl<std::thread::_Invoker<std::tuple<Ichor::AsioContextService::start(Ichor::AsioContextService::start()::_ZN5Ichor18AsioContextService5startEv.Frame*)::<lambda()> > > >::_M_run(void) (this=0x7b08000009c0) at /usr/include/c++/11/bits/std_thread.h:211  
#19 0x00007ffff70dc2b3 in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#20 0x00007ffff742e600 in __tsan_thread_start_func (arg=0x7fffffffc6e0) at ../../../../src/libsanitizer/tsan/tsan_interceptors_posix.cpp:959  
#21 0x00007ffff6c94b43 in start_thread (arg=<optimized out>) at ./nptl/pthread_create.c:442  
#22 0x00007ffff6d26a00 in clone3 () at ../sysdeps/unix/sysv/linux/x86_64/clone3.S:81  
  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2023-01-08 21:07:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1374927484  

I'm 80% certain this is a bug in tsan

---

## Comment 10

> Username: Oipo  
> Created at: 2023-01-12 19:05:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1380877232  

I think we can make it a 100%. Compiling with gcc 12.2:  
```  
    inlined from ‘boost::asio::detail::std_fenced_block::~std_fenced_block()’ at /usr/include/boost/asio/detail/std_fenced_block.hpp:52:29:  
/opt/gcc/12/include/c++/12.2.0/bits/atomic_base.h:133:26: warning: ‘atomic_thread_fence’ is not supported with ‘-fsanitize=thread’ [-Wtsan]  
```

---

## Comment 11

> Username: Oipo  
> Created at: 2023-01-12 19:11:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1380883171  

[This Zig issue](https://github.com/ziglang/zig/issues/11650) talks about various options for their project. Would it be worthwhile for me to investigate using standard atomic stores/loads instead of fences if boost.asio is compiled with thread sanitizer?

---

## Comment 12

> Username: ashtum  
> Created at: 2023-08-18 13:45:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1683944899  

@Oipo Is this still open?

---

## Comment 13

> Username: Oipo  
> Created at: 2023-08-18 15:55:28 UTC  
> Updated at: 2023-08-18 15:55:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1684117050  

@ashtum yes. Thread sanitizer doesn't support `std::atomic_thread_fence`, which is [still used in asio](https://github.com/boostorg/asio/blob/28ff1e7c2b44f141eb809abaf2d76c95f38350ca/include/boost/asio/detail/std_fenced_block.hpp#L46).   
  
I don't remember the outcome of me testing it earlier this year, but if my memory serves correcly, switching the `std::atomic_thread_fence` to an `std::atomic<bool>::store` with a seq_cst order 'fixes' it in the sense that tsan stops complaining. I am not very sure that that is actually the intended behaviour of `fenced_block` though.

---

## Comment 14

> Username: ashtum  
> Created at: 2023-08-18 16:58:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2615#issuecomment-1684189706  

> @ashtum yes. Thread sanitizer doesn't support `std::atomic_thread_fence`, which is [still used in asio](https://github.com/boostorg/asio/blob/28ff1e7c2b44f141eb809abaf2d76c95f38350ca/include/boost/asio/detail/std_fenced_block.hpp#L46).  
>   
> I don't remember the outcome of me testing it earlier this year, but if my memory serves correcly, switching the `std::atomic_thread_fence` to an `std::atomic<bool>::store` with a seq_cst order 'fixes' it in the sense that tsan stops complaining. I am not very sure that that is actually the intended behaviour of `fenced_block` though.  
  
Yes, I can remember having a similar problem with asio::strand destructors and sanitizers. in [this line](https://github.com/chriskohlhoff/asio/blob/89b0a4138a92883ae2514be68018a6c837a5b65f/asio/include/asio/execution/any_executor.hpp#L556).  
It is clearly a false positive because sanitizers have trouble with judging about standalone fences.  
In case of Asio the code is:  
```C++  
inline bool ref_count_down(atomic_count& a)  
{  
  if (a.fetch_sub(1, std::memory_order_release) == 1)  
  {  
    std::atomic_thread_fence(std::memory_order_acquire);  
    return true;  
  }  
  return false;  
}  
```  
If we change it to:  
```C++  
inline bool ref_count_down(atomic_count& a)  
{  
  if (a.fetch_sub(1, std::memory_order_acq_rel) == 1) // <-- note using memory_order_acq_rel   
  {  
    // std::atomic_thread_fence(std::memory_order_acquire);  
    return true;  
  }  
  return false;  
}  
```  
The problem goes away.  
  
This is known issue in standard library implementations too, they use some workaround to get ride of it:  
https://github.com/gcc-mirror/gcc/blob/53fcd3bdbc9104a8acdc79134d8d272d7eead0fd/libstdc%2B%2B-v3/include/bits/shared_ptr_base.h#L311  
https://github.com/gcc-mirror/gcc/blob/53fcd3bdbc9104a8acdc79134d8d272d7eead0fd/libstdc%2B%2B-v3/include/bits/shared_ptr_base.h#L189
