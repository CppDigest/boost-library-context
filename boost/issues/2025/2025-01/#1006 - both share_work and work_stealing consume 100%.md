# #1006 - both share_work and work_stealing consume 100% [Closed]

> Username: robaho  
> Created at: 2025-01-28 18:48:58 UTC  
> Updated at: 2025-01-29 16:53:27 UTC  
> Closed at: 2025-01-29 16:53:26 UTC  
> Url: https://github.com/boostorg/boost/issues/1006  

The system is running OSX 15.2.  
  
The system is idle until some work is scheduled. When the work completes, the scheduler still spins. This doesn't happen with round_robin, only share_work and work_strealing. I don't believe there are any fibers executing and the samples bear that out. I am running 4 worker threads - and they all have the same stack trace. Does the `dispatch()` call mean the scheduler found some work? Must all fibers be `joined` to clean them from the scheduler (rather than simply returning).  
  
Any ideas how to diagnose? Thanks for your help.  
  
The samples look like:  
  
  
```  
    2380 Thread_66050974  
    + 2380 trampoline  (in libboost_context.dylib) + 3  [0x1090e3a2f]  
    +   2380 boost::context::detail::fiber_entry<boost::context::detail::fiber_record<boost::context::fiber, boost::fibers::stack_allocator_wrapper, std::__bind<boost::context::fiber (boost::fibers::dispatcher_context::*)(boost::context::fiber&&), boost::fibers::dispatcher_context*, std::placeholders::__ph<1> const&>>>(boost::context::detail::transfer_t)  (in libboost_fiber.dylib) + 61  [0x1090fdd7d]  
    +     2380 boost::fibers::dispatcher_context::run_(boost::context::fiber&&)  (in libboost_fiber.dylib) + 18  [0x1090fdd02]  
    +       1925 boost::fibers::scheduler::dispatch()  (in libboost_fiber.dylib) + 151  [0x1091006a7]  
    +       ! 1382 boost::fibers::algo::shared_work::pick_next()  (in libboost_fiber.dylib) + 22  [0x1090fa036]  
    +       ! : 1379 std::mutex::lock()  (in libc++.1.dylib) + 9  [0x7ff8077fbc79]  
    +       ! : | 1258 _pthread_mutex_firstfit_lock_slow  (in libsystem_pthread.dylib) + 214  [0x7ff8078b8921]  
    +       ! : | + 1238 _pthread_mutex_firstfit_lock_wait  (in libsystem_pthread.dylib) + 76  [0x7ff8078bab14]  
    +       ! : | + ! 1238 __psynch_mutexwait  (in libsystem_kernel.dylib) + 10,0,...  [0x7ff807880822,0x7ff807880818,...]  
    +       ! : | + 20 _pthread_mutex_firstfit_lock_wait  (in libsystem_pthread.dylib) + 76,80,...  [0x7ff8078bab14,0x7ff8078bab18,...]  
    +       ! : | 84 pthread_mutex_lock  (in libsystem_pthread.dylib) + 105,11,...  [0x7ff8078b853e,0x7ff8078b84e0,...]  
    +       ! : | 35 _pthread_mutex_firstfit_lock_slow  (in libsystem_pthread.dylib) + 183,152,...  [0x7ff8078b8902,0x7ff8078b88e3,...]  
    +       ! : | 2 DYLD-STUB$$pthread_mutex_lock  (in libc++.1.dylib) + 0  [0x7ff807841144]  
    +       ! : 2 std::mutex::lock()  (in libc++.1.dylib) + 0,13  [0x7ff8077fbc70,0x7ff8077fbc7d]  
    +       ! : 1 DYLD-STUB$$std::mutex::lock()  (in libboost_fiber.dylib) + 0  [0x109100f66]  
    +       ! 524 boost::fibers::algo::shared_work::pick_next()  (in libboost_fiber.dylib) + 169  [0x1090fa0c9]  
    +       ! : 519 std::mutex::unlock()  (in libc++.1.dylib) + 9  [0x7ff8077fbcc5]  
    +       ! : | 464 _pthread_mutex_firstfit_unlock_slow  (in libsystem_pthread.dylib) + 243  [0x7ff8078b8b57]  
    +       ! : | + 460 _pthread_mutex_firstfit_wake  (in libsystem_pthread.dylib) + 20  [0x7ff8078baba2]  
    +       ! : | + ! 460 __psynch_mutexdrop  (in libsystem_kernel.dylib) + 10  [0x7ff80788095a]  
    +       ! : | + 4 _pthread_mutex_firstfit_wake  (in libsystem_pthread.dylib) + 20  [0x7ff8078baba2]  
    +       ! : | 47 pthread_mutex_unlock  (in libsystem_pthread.dylib) + 90,11,...  [0x7ff8078b89b3,0x7ff8078b8964,...]  
    +       ! : | 6 _pthread_mutex_firstfit_unlock_slow  (in libsystem_pthread.dylib) + 117,1,...  [0x7ff8078b8ad9,0x7ff8078b8a65,...]  
    +       ! : | 2 DYLD-STUB$$pthread_mutex_unlock  (in libc++.1.dylib) + 0  [0x7ff807841150]  
    +       ! : 3 DYLD-STUB$$std::mutex::unlock()  (in libboost_fiber.dylib) + 0  [0x109100f6c]  
    +       ! : 2 std::mutex::unlock()  (in libc++.1.dylib) + 0  [0x7ff8077fbcbc]  
    +       ! 19 boost::fibers::algo::shared_work::pick_next()  (in libboost_fiber.dylib) + 173,0,...  [0x1090fa0cd,0x1090fa020,...]  
    +       211 boost::fibers::scheduler::dispatch()  (in libboost_fiber.dylib) + 124  [0x10910068c]  
    +       ! 203 std::chrono::steady_clock::now()  (in libc++.1.dylib) + 22  [0x7ff8077fa152]  
    +       ! : 188 clock_gettime  (in libsystem_c.dylib) + 47  [0x7ff8077743b1]  
    +       ! : | 122 clock_gettime_nsec_np  (in libsystem_c.dylib) + 282  [0x7ff80776567e]  
    +       ! : | + 112 mach_continuous_time  (in libsystem_kernel.dylib) + 20  [0x7ff80787e89d]  
    +       ! : | + ! 75 _mach_continuous_time  (in libsystem_kernel.dylib) + 41  [0x7ff80788c1dd]  
    +       ! : | + ! : 75 mach_absolute_time  (in libsystem_kernel.dylib) + 28,26,...  [0x7ff80787e8c3,0x7ff80787e8c1,...]  
    +       ! : | + ! 37 _mach_continuous_time  (in libsystem_kernel.dylib) + 32,45,...  [0x7ff80788c1d4,0x7ff80788c1e1,...]  
    +       ! : | + 8 mach_continuous_time  (in libsystem_kernel.dylib) + 23,5,...  [0x7ff80787e8a0,0x7ff80787e88e,...]  
    +       ! : | + 2 DYLD-STUB$$mach_continuous_time  (in libsystem_c.dylib) + 0  [0x7ff8077e4684]  
    +       ! : | 52 clock_gettime_nsec_np  (in libsystem_c.dylib) + 352,0,...  [0x7ff8077656c4,0x7ff807765564,...]  
    +       ! : | 14 clock_gettime_nsec_np  (in libsystem_c.dylib) + 234  [0x7ff80776564e]  
    +       ! : |   13 mach_timebase_info  (in libsystem_kernel.dylib) + 5,15,...  [0x7ff80787e847,0x7ff80787e851,...]  
    +       ! : |   1 DYLD-STUB$$mach_timebase_info  (in libsystem_c.dylib) + 0  [0x7ff8077e469c]  
    +       ! : 14 clock_gettime  (in libsystem_c.dylib) + 79,72,...  [0x7ff8077743d1,0x7ff8077743ca,...]  
    +       ! : 1 DYLD-STUB$$clock_gettime  (in libc++.1.dylib) + 0  [0x7ff807840fe8]  
    +       ! 8 std::chrono::steady_clock::now()  (in libc++.1.dylib) + 8,34,...  [0x7ff8077fa144,0x7ff8077fa15e,...]  
    +       208 boost::fibers::scheduler::dispatch()  (in libboost_fiber.dylib) + 119  [0x109100687]  
    +       ! 145 boost::fibers::scheduler::remote_ready2ready_()  (in libboost_fiber.dylib) + 24,213,...  [0x109100098,0x109100155,...]  
    +       ! 63 boost::fibers::scheduler::remote_ready2ready_()  (in libboost_fiber.dylib) + 47  [0x1091000af]  
    +       !   51 boost::fibers::detail::spinlock_ttas::lock()  (in libboost_fiber.dylib) + 77,32,...  [0x1090fbcfd,0x1090fbcd0,...]  
    +       !   7 boost::fibers::detail::spinlock_ttas::lock()  (in libboost_fiber.dylib) + 50  [0x1090fbce2]  
    +       !   | 7 tlv_get_addr  (in libdyld.dylib) + 0,18  [0x7ff8078c461d,0x7ff8078c462f]  
    +       !   5 boost::fibers::detail::spinlock_ttas::lock()  (in libboost_fiber.dylib) + 29  [0x1090fbccd]  
    +       !     5 tlv_get_addr  (in libdyld.dylib) + 13,0  [0x7ff8078c462a,0x7ff8078c461d]  
    +       14 boost::fibers::scheduler::dispatch()  (in libboost_fiber.dylib) + 111  [0x10910067f]  
    +       ! 14 boost::fibers::scheduler::release_terminated_()  (in libboost_fiber.dylib) + 15,6,...  [0x1090fffbf,0x1090fffb6,...]  
    +       12 boost::fibers::scheduler::dispatch()  (in libboost_fiber.dylib) + 111,174,...  [0x10910067f,0x1091006be,...]  
    +       10 boost::fibers::scheduler::dispatch()  (in libboost_fiber.dylib) + 201  [0x1091006d9]  
    +         10 boost::fibers::algo::shared_work::suspend_until(std::chrono::time_point<std::chrono::steady_clock, std::chrono::duration<long long, std::ratio<1l, 1000000000l>>> const&)  (in libboost_fiber.dylib) + 0,4,...    
```

---

## Comment 1

> Username: robaho  
> Created at: 2025-01-29 16:53:26 UTC  
> Url: https://github.com/boostorg/boost/issues/1006#issuecomment-2622188816  

ok, I figured out you need to pass 'true' as the suspend option to the scheduler. Seems like this should be the default, or at least documented more prominently.
