# #900 - The futex facility returned an unexpected error code. [Closed]

> Username: zhaocc1106  
> Created at: 2024-05-06 06:39:45 UTC  
> Updated at: 2024-05-16 01:38:56 UTC  
> Closed at: 2024-05-16 01:38:56 UTC  
> Url: https://github.com/boostorg/boost/issues/900  

When i use boost threadpool to post task and stress test like:  
```  
boost::asio::post(*worker_tp_, [this, tasks = std::move(tasks)]() mutable {  
        std::vector<const ::grps::protos::v1::GrpsMessage*> inputs;  
        std::vector<::grps::protos::v1::GrpsMessage*> outputs;  
        std::vector<GrpsContext*> ctxs;  
        for (const auto& task : tasks) {  
          inputs.emplace_back(task.input);  
          outputs.emplace_back(task.output);  
          ctxs.emplace_back(task.ctx == nullptr ? task.ctx_sp.get() : task.ctx);  
        }  
        BatchInferProcess(inputs, outputs, ctxs);  
      });  
```  
  
There is the following core dump after some hours.  
```  
#0 raise (sig=<optimized out>) at ../sysdeps/unix/sysv/linux/raise.c:51  
#1 0x00005641690a8d90 in backward::SignalHandling::sig_handler (signo=<optimized out>, info=0x7f232bdb93b0, _ctx=<optimized out>) at /root/mygrps/cpp_server/src/common/backward.hpp:4299  
#2 <signal handler called>  
#3 __GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:51  
#4 0x00007f235547a7f1 in __GI_abort () at abort.c:79  
#5 0x00007f23554c3585 in __libc_message (action=(do_abort | do_backtrace), fmt=0x7f23555f0677 "%s", fmt=0x7f23555f0677 "%s", action=(do_abort | do_backtrace)) at ../sysdeps/posix/libc_fatal.c:181  
#6 0x00007f23554c38ca in __GI___libc_fatal (message=message@entry=0x7f23a7c24280 "The futex facility returned an unexpected error code.") at ../sysdeps/posix/libc_fatal.c:191  
#7 0x00007f23a7c1dbca in futex_fatal_error () at ../sysdeps/nptl/futex-internal.h:200  
#8 futex_wake (private=<optimized out>, processes_to_wake=<optimized out>, futex_word=<optimized out>) at ../sysdeps/unix/sysv/linux/futex-internal.h:247  
#9 __condvar_confirm_wakeup (private=<optimized out>, cond=<optimized out>) at pthread_cond_wait.c:55  
#10 __pthread_cond_wait_common (abstime=0x0, mutex=0x7f234fff9238, cond=0x7f234fff9270) at pthread_cond_wait.c:419  
#11 __pthread_cond_wait (cond=0x7f234fff9270, mutex=0x7f234fff9238) at pthread_cond_wait.c:655  
#12 0x000056416909d486 in boost::asio::detail::posix_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (lock=…, this=<optimized out>)  
at /usr/local/include/boost/asio/detail/posix_event.hpp:119  
#13 boost::asio::detail::conditionally_enabled_event::wait (lock=…, this=0x7f234fff9268) at /usr/local/include/boost/asio/detail/conditionally_enabled_event.hpp:97  
#14 boost::asio::detail::scheduler::do_run_one (ec=…, this_thread=…, lock=…, this=0x7f234fff9200) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:502  
#15 boost::asio::detail::scheduler::run (this=0x7f234fff9200, ec=…) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:210  
#16 0x000056416909da89 in boost::asio::thread_pool::thread_function::operator() (this=<optimized out>) at /usr/local/include/boost/asio/impl/thread_pool.ipp:39  
#17 boost::asio::detail::posix_thread::func<boost::asio::thread_pool::thread_function>::run (this=<optimized out>) at /usr/local/include/boost/asio/detail/posix_thread.hpp:86  
#18 0x00005641690973bf in boost::asio::detail::boost_asio_detail_posix_thread_function (arg=0x7f234e4243c0) at /usr/local/include/boost/asio/detail/impl/posix_thread.ipp:74  
#19 0x00007f23a7c176db in start_thread (arg=0x7f232bdc4700) at pthread_create.c:463  
#20 0x00007f235555b61f in clone () at ../sysdeps/unix/sysv/linux/x86_64/clone.S:95  
```  
Is the bug of boost thread pool ?
