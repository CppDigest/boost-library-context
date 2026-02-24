# #279 - segfault when context switching in boost 1.76 [Closed]

> Username: romange  
> Created at: 2021-07-28 18:49:48 UTC  
> Updated at: 2021-07-28 18:53:16 UTC  
> Closed at: 2021-07-28 18:53:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/279  

Sometimes I get segfault inside fiber library.  
  
Below is a stacktrace that shows it  
  
```  
#0  boost::fibers::context::resume (this=0x0, lk=...) at libs/fiber/src/context.cpp:153  
#1  0x00007ffff7fbabee in boost::fibers::scheduler::suspend (this=<optimized out>, lk=...) at libs/fiber/src/scheduler.cpp:314  
#2  0x00007ffff7fb71dd in boost::fibers::context::suspend (this=this@entry=0xa6a4386ff00, lk=...) at libs/fiber/src/context.cpp:181  
#3  0x00007ffff7fb851c in boost::fibers::wait_queue::suspend_and_wait (this=this@entry=0xa6a421609c8, lk=..., active_ctx=active_ctx@entry=0xa6a4386ff00) at libs/fiber/src/waker.cpp:21  
```

---

## Comment 1

> Username: romange  
> Created at: 2021-07-28 18:53:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/279#issuecomment-888541973  

sorry, probably a problem with my scheduling algorithm.
