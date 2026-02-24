# #340 - Example fib_task_region.cpp gets undeterminized result [Open]

> Username: yuxianch  
> Created at: 2021-05-24 08:21:00 UTC  
> Updated at: 2021-05-24 08:24:37 UTC  
> Url: https://github.com/boostorg/thread/issues/340  

[fib_task_region.cpp](https://github.com/boostorg/thread/blob/4abafccff4bdeb4b5ac516ff0c2bc7c0dad8bafb/example/fib_task_region.cpp) is randomly failed with clang++(latest version) on linux platform.  
https://github.com/boostorg/thread/blob/4abafccff4bdeb4b5ac516ff0c2bc7c0dad8bafb/example/fib_task_region.cpp#L72-L74  
Use gdb to show backtrace(the breaking point is consistent):  
```  
...  
[Thread 0x7c65db40 (LWP 2168013) exited]  
ex_fib_task_region: ../../../boost/thread/pthread/condition_variable_fwd.hpp:81: boost::condition_variable::~condition_variable(): Assertion `!posix::pthread_mutex_destroy(&internal_mutex)' failed.  
ex_fib_task_region: ../../../boost/thread/pthread/condition_variable_fwd.hpp:81: boost::condition_variable::~condition_variable(): Assertion `!posix::pthread_mutex_destroy(&internal_mutex)' failed.  
[Thread 0x1c442b40 (LWP 2168205) exited]  
...  
[Thread 0x744f2b40 (LWP 2168029) exited]  
[Thread 0x78cfbb40 (LWP 2168020) exited]  
[Thread 0x764f6b40 (LWP 2168025) exited]  
  
Thread 1 "ex_fib_task_reg" received signal SIGABRT, Aborted.  
0xf7fd2049 in __kernel_vsyscall ()  
(gdb) bt  
#0  0xf7fd2049 in __kernel_vsyscall ()  
#1  0xf76c0436 in raise () from /lib/libc.so.6  
#2  0xf76a9bc4 in abort () from /lib/libc.so.6  
#3  0xf76a9acb in __assert_fail_base.cold.0 () from /lib/libc.so.6  
#4  0xf76b81cf in __assert_fail () from /lib/libc.so.6  
#5  0x08071035 in boost::condition_variable::~condition_variable (this=0xffff81e0) at ../../../boost/thread/pthread/condition_variable_fwd.hpp:81  
#6  0x08077b4b in boost::concurrent::detail::sync_queue_base<boost::detail::nullary_function<void ()>, boost::csbl::devector<boost::detail::nullary_function<void ()> > >::~sync_queue_base() (this=0xffff81c8)  
    at ../../../boost/thread/concurrent_queues/detail/sync_queue_base.hpp:116  
#7  0x0807095f in boost::concurrent::sync_queue<boost::detail::nullary_function<void ()>, boost::csbl::devector<boost::detail::nullary_function<void ()> > >::~sync_queue() (this=0xffff81c8) at ../../../boost/thread/concurrent_queues/sync_queue.hpp:136  
#8  0x080514b2 in boost::executors::basic_thread_pool::basic_thread_pool (this=0xffff81bc, thread_count=81) at ../../../boost/thread/executors/basic_thread_pool.hpp:161  
#9  0x0807a059 in boost::experimental::parallel::v2::task_region_handle::task_region_handle (this=0xffff8180) at ../../../boost/thread/experimental/parallel/v2/task_region.hpp:259  
#10 0x0804f090 in boost::experimental::parallel::v2::task_region_final<fib_task_region(int)::$_0>(fib_task_region(int)::$_0&&) (f=...) at ../../../boost/thread/experimental/parallel/v2/task_region.hpp:295  
#11 0x0804ed8f in boost::experimental::parallel::v2::task_region<fib_task_region(int)::$_0>(fib_task_region(int)::$_0&&) (f=...) at ../../../boost/thread/experimental/parallel/v2/task_region.hpp:310  
#12 0x0804ed45 in fib_task_region (n=2) at ../example/fib_task_region.cpp:30  
#13 0x0804f215 in fib_task_region(int)::$_0::operator()(boost::experimental::parallel::v2::task_region_handle&) const (this=0xffff83e8, trh=...) at ../example/fib_task_region.cpp:37  
#14 0x0804f0a5 in boost::experimental::parallel::v2::task_region_final<fib_task_region(int)::$_0>(fib_task_region(int)::$_0&&) (f=...) at ../../../boost/thread/experimental/parallel/v2/task_region.hpp:298  
#15 0x0804ed8f in boost::experimental::parallel::v2::task_region<fib_task_region(int)::$_0>(fib_task_region(int)::$_0&&) (f=...) at ../../../boost/thread/experimental/parallel/v2/task_region.hpp:310  
#16 0x0804ed45 in fib_task_region (n=4) at ../example/fib_task_region.cpp:30  
#17 0x0804f215 in fib_task_region(int)::$_0::operator()(boost::experimental::parallel::v2::task_region_handle&) const (this=0xffff8558, trh=...) at ../example/fib_task_region.cpp:37  
#18 0x0804f0a5 in boost::experimental::parallel::v2::task_region_final<fib_task_region(int)::$_0>(fib_task_region(int)::$_0&&) (f=...) at ../../../boost/thread/experimental/parallel/v2/task_region.hpp:298  
#19 0x0804ed8f in boost::experimental::parallel::v2::task_region<fib_task_region(int)::$_0>(fib_task_region(int)::$_0&&) (f=...) at ../../../boost/thread/experimental/parallel/v2/task_region.hpp:310  
#20 0x0804ed45 in fib_task_region (n=6) at ../example/fib_task_region.cpp:30  
#21 0x0804ede6 in main () at ../example/fib_task_region.cpp:73  
```  
From the back trace info, something unexpected happens in line 295 of thread/experimental/parallel/v2/task_region.hpp and finally SIGABRT signal is triggered:  
https://github.com/boostorg/thread/blob/4abafccff4bdeb4b5ac516ff0c2bc7c0dad8bafb/include/boost/thread/experimental/parallel/v2/task_region.hpp#L292-L305
