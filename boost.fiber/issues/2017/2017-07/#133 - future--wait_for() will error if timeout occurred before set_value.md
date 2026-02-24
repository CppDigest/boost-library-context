# #133 - future::wait_for() will error if timeout occurred before set_value [Closed]

> Username: wanghengwei  
> Created at: 2017-07-07 05:39:15 UTC  
> Updated at: 2017-07-12 07:13:48 UTC  
> Closed at: 2017-07-12 07:13:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/133  

compiler: vc2017 WIN32  
boost: 1.64 --with-context --with-fiber asmflags=/safeseh  
code:  
```c++  
    boost::fibers::promise<void> p; // 1  
      
    boost::fibers::fiber f(boost::fibers::launch::dispatch, [&p]() mutable {  
        auto f = p.get_future(); // 2  
        auto r = f.wait_for(std::chrono::seconds{0}); // 3  
    });  
    p.set_value(); //4 crash!  
    boost::this_fiber::yield(); //5  
  
    f.join();  
```  
  
error message:  
```  
Assertion failed: ! ctx->ready_is_linked(), file libs\fiber\src\scheduler.cpp, line 223  
```

---

## Comment 1

> Username: olk  
> Created at: 2017-07-08 15:55:02 UTC  
> Updated at: 2017-07-08 15:56:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/133#issuecomment-313864363  

works for me with version in branch develop (master should be on the same stage)  
tested on Win10 + MSVC 14.1 / Linux gcc-7.1.1/clang-4.0.1  
  
 could you please verify it?! please update boost.fiber + boost.context too from branch develop/master

---

## Comment 2

> Username: olk  
> Created at: 2017-07-12 07:13:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/133#issuecomment-314676749  

works for me
