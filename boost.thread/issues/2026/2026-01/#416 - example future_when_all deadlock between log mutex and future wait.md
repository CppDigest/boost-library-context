# #416 - example future_when_all deadlock between log mutex and future wait [Open]

> Username: baiwfg2  
> Created at: 2026-01-10 11:12:29 UTC  
> Updated at: 2026-01-10 11:12:29 UTC  
> Url: https://github.com/boostorg/thread/issues/416  

Hi. I ran the `future_when_all.cpp` example with `BOOST_THREAD_USES_LOG` on. And see the following deadlock:  
  
```  
# pstack `pidof boost-thread`  
Thread 2 (Thread 0x7fefed7406c0 (LWP 58475) "boost-thread"):  
#0  0x00007fefee7caf50 in __lll_lock_wait () from /lib64/libc.so.6  
#1  0x00007fefee7d146a in pthread_mutex_lock@@GLIBC_2.2.5 () from /lib64/libc.so.6  
#2  0x00000000004110a8 in boost::posix::pthread_mutex_lock (m=0x451500 <boost::thread_detail::terminal_mutex()::mtx>) at /data/opt/boost/include/boost/thread/pthread/pthread_helpers.hpp:79  
#3  boost::recursive_mutex::lock (this=0x451500 <boost::thread_detail::terminal_mutex()::mtx>) at /data/opt/boost/include/boost/thread/pthread/recursive_mutex.hpp:108  
#4  0x0000000000413e18 in boost::lock_guard<boost::recursive_mutex>::lock_guard (this=0x7fefed73fc78, m_=...) at /data/opt/boost/include/boost/thread/lock_guard.hpp:38  
#5  0x00000000004067ba in p1b () at /mnt/cxx/boost/thread-example/future_when_all.cpp:41  
#6  0x000000000042758e in boost::detail::invoke<int (*)()> (f=@0x151f558: 0x40679a <p1b()>) at /data/opt/boost/include/boost/thread/detail/invoke.hpp:140  
#7  0x000000000042530c in boost::detail::invoker<int (*)()>::execute<>(boost::detail::tuple_indices<>) (this=0x151f558) at /data/opt/boost/include/boost/thread/detail/invoker.hpp:102  
#8  0x0000000000422f08 in boost::detail::invoker<int (*)()>::operator() (this=0x151f558) at /data/opt/boost/include/boost/thread/detail/invoker.hpp:95  
#9  0x00000000004204c1 in boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> >::run (that=..., f=...) at /data/opt/boost/include/boost/thread/future.hpp:981  
#10 0x000000000042f003 in boost::detail::invoke<void (*)(boost::shared_ptr<boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> > >, boost::detail::invoker<int (*)()>&&), boost::shared_ptr<boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> > >, boost::detail::invoker<int (*)()> > (f=@0x151f570: 0x420495 <boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> >::run(boost::shared_ptr<boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> > >, boost::detail::invoker<int (*)()>&&)>) at /data/opt/boost/include/boost/thread/detail/invoke.hpp:140  
#11 0x000000000042e9cd in boost::detail::thread_data<void (*)(boost::shared_ptr<boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> > >, boost::detail::invoker<int (*)()>&&), boost::shared_ptr<boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> > >, boost::detail::invoker<int (*)()> >::run2<1ul, 2ul> (this=0x151f420) at /data/opt/boost/include/boost/thread/detail/thread.hpp:79  
#12 0x000000000042c126 in boost::detail::thread_data<void (*)(boost::shared_ptr<boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> > >, boost::detail::invoker<int (*)()>&&), boost::shared_ptr<boost::detail::future_async_shared_state<int, boost::detail::invoker<int (*)()> > >, boost::detail::invoker<int (*)()> >::run (this=0x151f420) at /data/opt/boost/include/boost/thread/detail/thread.hpp:85  
#13 0x00007fefeed5d3e0 in thread_proxy () from /data/opt/boost/lib/libboost_thread.so.1.90.0  
#14 0x00007fefee7ce12d in start_thread () from /lib64/libc.so.6  
#15 0x00007fefee84fbc0 in clone3 () from /lib64/libc.so.6  
  
Thread 1 (Thread 0x7fefeeca7000 (LWP 58458) "boost-thread"):  
#0  0x00007fefee7cad16 in __futex_abstimed_wait_common () from /lib64/libc.so.6  
#1  0x00007fefee7cd510 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libc.so.6  
#2  0x000000000040e51d in boost::posix::pthread_cond_wait (m=0x151ef18, c=0x151ef40) at /data/opt/boost/include/boost/thread/pthread/pthread_helpers.hpp:112  
#3  boost::condition_variable::wait (this=0x151ef18, m=...) at /data/opt/boost/include/boost/thread/pthread/condition_variable.hpp:79  
#4  0x0000000000413239 in boost::condition_variable::wait<boost::_bi::bind_t<boost::_bi::unspecified, boost::_mfi::mf<bool (boost::detail::shared_state_base::*)(), bool, boost::detail::shared_state_base>, boost::_bi::list<boost::reference_wrapper<boost::detail::shared_state_base> > > > (this=0x151ef18, m=..., pred=...) at /data/opt/boost/include/boost/thread/pthread/condition_variable_fwd.hpp:93  
#5  0x00000000004102d9 in boost::detail::shared_state_base::wait_internal (this=0x151eec0, lk=..., rethrow=true) at /data/opt/boost/include/boost/thread/future.hpp:395  
#6  0x000000000041c74e in boost::detail::shared_state<int>::get_storage (this=0x151eec0, lk=...) at /data/opt/boost/include/boost/thread/future.hpp:647  
#7  0x0000000000418177 in boost::detail::shared_state<int>::get (this=0x151eec0, lk=...) at /data/opt/boost/include/boost/thread/future.hpp:652  
#8  0x00000000004140fe in boost::future<int>::get (this=0x7ffc9ee4dda0) at /data/opt/boost/include/boost/thread/future.hpp:1804  
#9  0x0000000000409103 in t_future_when_all () at /mnt/cxx/boost/thread-example/future_when_all.cpp:238  
#10 0x000000000040668c in main (argc=2, argv=0x7ffc9ee4e758) at /mnt/cxx/boost/thread-example/thread_example.cc:6  
  
```  
  
Maybe we should split the operation:  
> BOOST_THREAD_LOG          << res[1].get()  
  
into two separate statements:  
> auto i = res[1].get();  
BOOST_THREAD_LOG << i
