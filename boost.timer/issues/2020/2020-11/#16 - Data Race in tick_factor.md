# #16 - Data Race in tick_factor [Closed]

> Username: jlaxson  
> Created at: 2020-11-19 00:17:56 UTC  
> Updated at: 2021-03-04 03:54:40 UTC  
> Closed at: 2021-03-04 03:54:40 UTC  
> Url: https://github.com/boostorg/timer/issues/16  

Clang ThreadSanitizer identifies a race in initializing tick_factor when multiple cpu_timers are created concurrently:  
  
```  
WARNING: ThreadSanitizer: data race (pid=44695)  
  Read of size 8 at 0x0001088354b8 by thread T15:  
    #0 (anonymous namespace)::get_cpu_times(boost::timer::cpu_times&) cpu_timer.cpp:102 (libboost_timer.1.72.0.dylib:x86_64+0x36f1)  
    #1 boost::timer::cpu_timer::start() cpu_timer.cpp:194 (libboost_timer.1.72.0.dylib:x86_64+0x3666)  
    #2 application frames that construct a cpu_timer  
  
  Previous write of size 8 at 0x0001088354b8 by thread T14:  
    #0 (anonymous namespace)::get_cpu_times(boost::timer::cpu_times&) cpu_timer.cpp:104 (libboost_timer.1.72.0.dylib:x86_64+0x3736)  
    #1 boost::timer::cpu_timer::start() cpu_timer.cpp:194 (libboost_timer.1.72.0.dylib:x86_64+0x3666)  
    #2 application frames that construct a cpu_timer  
  
  Location is global '(anonymous namespace)::tick_factor()::tick_factor' at 0x0001088354b8 (libboost_timer.1.72.0.dylib+0x0000000064b8)  
```  
  
The documentation mentions races briefly:  
> The Timer library meets the same data race avoidance requirements as the C++11 standard library (17.6.5.9 [res.on.data.races]). Shared objects of Timer library types risk undefined behavior unless the user supplies a locking mechanism. See C++11, 17.6.4.10 [res.on.objects]  
  
But I see this issue with separate, thread-private instances that race when initializing `tick_factor()`'s static storage.  
  
Having parallel CPU timers is definitely not going to yield the most interesting data, but seems like this can/should be safe regardless.  Would declaring `tick_factor` with thread_local instead of static storage (when targeting C++11 and up) be an acceptable change?

---

## Comment 1

> Username: pdimov  
> Created at: 2020-11-20 02:12:59 UTC  
> Url: https://github.com/boostorg/timer/issues/16#issuecomment-730800605  

Should be fixed on develop.
