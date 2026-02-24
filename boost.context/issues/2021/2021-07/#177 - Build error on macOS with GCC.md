# #177 - Build error on macOS with GCC [Closed]

> Username: eschnett  
> Created at: 2021-07-14 21:23:30 UTC  
> Updated at: 2021-07-15 04:44:59 UTC  
> Closed at: 2021-07-15 04:44:59 UTC  
> Url: https://github.com/boostorg/context/issues/177  

I want to build Boost with its context library on macOS using GCC. I see the following error in the configure state:  
```  
error: No best alternative for libs/context/build/asm_sources  
```  
Building Boost then continues and seems to succeed. However, when linking against the resulting libraries, there are unresolved symbols  
```  
Undefined symbols for architecture x86_64:  
  "_jump_fcontext", referenced from:  
      __ZN3hpx7threads10coroutines6detail12context_baseINS2_14coroutine_implEE6invokeEv in context_base.cpp.o  
      __ZN3hpx7threads10coroutines6detail12context_baseINS2_14coroutine_implEE5yieldEv in context_base.cpp.o  
      __ZN3hpx7threads10coroutines6detail12context_baseINS2_14coroutine_implEE9do_returnENS5_19context_exit_statusEONSt15__exception_ptr13exception_ptrE in context_base.cpp.o  
      __ZN3hpx7threads10coroutines6detail12context_baseINS2_14coroutine_implEE8do_yieldEv in context_base.cpp.o  
      __ZN3hpx7threads10coroutines6detail12context_baseINS2_14coroutine_implEE9do_invokeEv in context_base.cpp.o  
      __ZN3hpx7threads10coroutines6detail23coroutine_stackful_self10yield_implESt4pairINS0_21thread_schedule_stateENS0_9thread_idEE in coroutine_impl.cpp.o  
      __ZN3hpx7threads10coroutines6detail14coroutine_implclEv in coroutine_impl.cpp.o  
      ...  
  "_make_fcontext", referenced from:  
      __ZN3hpx7threads10coroutines6detail12context_baseINS2_14coroutine_implEE6invokeEv in context_base.cpp.o  
      __ZN3hpx7threads11thread_dataclEPNS_14execution_base11this_thread6detail13agent_storageE in scheduled_thread_pool.cpp.o  
      __ZN3hpx7threads20thread_data_stackful6rebindERNS0_16thread_init_dataE in thread_data_stackful.cpp.o  
ld: symbol(s) not found for architecture x86_64  
collect2: error: ld returned 1 exit status  
```  
See e.g. https://github.com/STEllAR-GROUP/hpx/issues/5442 where I reported this initially.
