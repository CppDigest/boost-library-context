# #189 - Can a Buffered Channel be shared among threads ? [Closed]

> Username: KaoCC  
> Created at: 2018-12-22 02:10:36 UTC  
> Updated at: 2018-12-22 02:15:55 UTC  
> Closed at: 2018-12-22 02:15:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/189  

I followed the sample code from [this example](https://www.boost.org/doc/libs/1_69_0/libs/fiber/doc/html/fiber/worker.html) to create a producer/consumer pattern. However, when I tried to share the buffered channel to more threads the following error will occur:  
  
```  
* thread #2, stop reason = EXC_BAD_ACCESS (code=EXC_I386_GPFLT)  
    frame #0: 0x000000010000b963 Test`boost::fibers::detail::spinlock_ttas::lock() [inlined] std::__1::__atomic_base<boost::fibers::detail::spinlock_status, false>::load(this=0x4000000010030049, __m=memory_order_relaxed) const at atomic:929  
   926 	    _LIBCPP_INLINE_VISIBILITY  
   927 	    _Tp load(memory_order __m = memory_order_seq_cst) const _NOEXCEPT  
   928 	      _LIBCPP_CHECK_LOAD_MEMORY_ORDER(__m)  
-> 929 	        {return __c11_atomic_load(&__a_, __m);}  
   930 	    _LIBCPP_INLINE_VISIBILITY  
   931 	    operator _Tp() const volatile _NOEXCEPT {return load();}  
   932 	    _LIBCPP_INLINE_VISIBILITY  
Target 0: (Test) stopped.  
  
```  
  
I did something like:  
  
```  
 for (auto i = 0; i < num_thread; ++i) {  
  workers.push_back(std::thread(thread_worker(*this)));  
}  
```  
  
where the `thread_worker` is a callable that contains:  
  
```   
void operator()() { for (auto i = 0; i < FIBER_COUNT; ++i) {  
  boost::fibers::fiber([this]{process_task();}).detach();  
}  
  process_task();  
}  
```  
  
and   
  
```  
void process_task() {  
  task_t current_task;  
  while (hq.task_channel.pop(current_task) != boost::fibers::channel_op_status::closed) {  
    current_task();  
  }  
}  
```  
  
This is the build environment:  
  
CMake 3.13  
AppleClang 10.0.0.10001145  
Boost 1.68.0  
MacOS 10.13.6  
  
  
Thank you in advance.

---

## Comment 1

> Username: KaoCC  
> Created at: 2018-12-22 02:15:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/189#issuecomment-449538011  

Sorry. I just observed a mistake in my code right after I posted this one ...  
  
Problem resolved.
