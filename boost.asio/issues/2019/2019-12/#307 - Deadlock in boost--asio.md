# #307 - Deadlock in boost::asio [Closed]

> Username: Harry327  
> Created at: 2019-12-12 14:30:46 UTC  
> Updated at: 2020-12-30 01:11:55 UTC  
> Closed at: 2020-12-30 01:11:54 UTC  
> Url: https://github.com/boostorg/asio/issues/307  

Hi,  
It seems that there is a deadlock in boost::asio. At highload it may occur once a year, sometimes once a day, or it may never occurs. I use boost asio as server that accepts TCP connections, one io_service and many threads, async mode.  
I checked all handlers, after deadlock all my handlers were successfully completed, and there are pending operations.  
Recv-Q = 129  
All io_service threads locked in   
`futex(0x7f5434000e6c, FUTEX_WAIT_PRIVATE, 0, NULL`  
Here is gdb output after deadlock, all io_service threads have the same:  
```  
#0 0x00007f5444f8d8ad in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
No symbol table info available.  
#1 0x000000000045ab7c in boost::asio::detail::posix_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (lock=..., this=<optimized out>) at /usr/local/include/boost/asio/detail/posix_event.hpp:106  
lock = <optimized out>  
this = <optimized out>  
#2 boost::asio::detail::conditionally_enabled_event::wait (lock=..., this=0x7f5434000e38) at /usr/local/include/boost/asio/detail/conditionally_enabled_event.hpp:89  
No locals.  
#3 boost::asio::detail::scheduler::do_run_one (ec=..., this_thread=..., lock=..., this=0x7f5434000dd0) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:455  
No locals.  
#4 boost::asio::detail::scheduler::run (this=0x7f5434000dd0, ec=...) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:200  
this_thread = {<boost::asio::detail::thread_info_base> = {<boost::asio::detail::noncopyable> = {<No data fields>}, reusable_memory_ = {0x0, 0x0, 0x0}}, private_op_queue = {<boost::asio::detail::noncopyable> = {<No data fields>}, front_ = 0x0, back_ = 0x0}, private_outstanding_work = 0}  
ctx = {<boost::asio::detail::noncopyable> = {<No data fields>}, key_ = 0x7f5434000dd0, value_ = 0x7f5424ff8e40, next_ = 0x0}  
lock = {<boost::asio::detail::noncopyable> = {<No data fields>}, mutex_ = @0x7f5434000e00, locked_ = true}  
n = 154139379  
#5 0x00000000005cd410 in boost::asio::io_context::run (this=<optimized out>) at /usr/local/include/boost/asio/impl/io_context.ipp:63  
ec = {val_ = 0, failed_ = false, cat_ = 0xac3660 <boost::system::system_category()::system_category_instance>}  
s = <optimized out>  
ec = <optimized out>  
s = <optimized out>  
  
```

---

## Comment 1

> Username: MechanicsMingYan  
> Created at: 2020-10-28 17:03:26 UTC  
> Url: https://github.com/boostorg/asio/issues/307#issuecomment-718075661  

I also encountered the same problem, can someone please help

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:11:53 UTC  
> Url: https://github.com/boostorg/asio/issues/307#issuecomment-752293278  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#734](https://github.com/chriskohlhoff/asio/issues/734).
