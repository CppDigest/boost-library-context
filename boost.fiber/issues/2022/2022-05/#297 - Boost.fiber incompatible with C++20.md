# #297 - Boost.fiber incompatible with C++20 [Closed]

> Username: j-stephan  
> Created at: 2022-05-10 12:49:39 UTC  
> Updated at: 2022-05-10 12:52:54 UTC  
> Closed at: 2022-05-10 12:52:54 UTC  
> Url: https://github.com/boostorg/fiber/issues/297  

We discovered that [alpaka's](https://github.com/alpaka-group/alpaka) Boost.fiber back-end breaks when enabling C++20 compilation:  
  
```  
In file included from /home/runner/work/alpaka/alpaka/example/bufferCopy/src/bufferCopy.cpp:18:  
In file included from /home/runner/work/alpaka/alpaka/include/alpaka/alpaka.hpp:18:  
In file included from /home/runner/work/alpaka/alpaka/include/alpaka/acc/AccCpuFibers.hpp:20:  
In file included from /home/runner/work/alpaka/alpaka/include/alpaka/block/sync/BlockSyncBarrierFiber.hpp:16:  
In file included from /home/runner/work/alpaka/alpaka/include/alpaka/core/Fibers.hpp:39:  
In file included from /home/runner/work/alpaka/alpaka/boost/boost/fiber/future.hpp:7:  
/home/runner/work/alpaka/alpaka/boost/boost/fiber/future/async.hpp:30:12: error: no member named 'result_of' in namespace 'std'  
using std::result_of;  
      ~~~~~^  
/home/runner/work/alpaka/alpaka/boost/boost/fiber/future/async.hpp:36:14: error: no template named 'result_of'  
    typename result_of<  
             ^  
/home/runner/work/alpaka/alpaka/boost/boost/fiber/future/async.hpp:44:22: error: no template named 'result_of'  
    typedef typename result_of<  
                     ^  
/home/runner/work/alpaka/alpaka/boost/boost/fiber/future/async.hpp:52:12: error: no viable conversion from returned value of type 'future<result_type>' (aka 'future<int>') to function return type 'int'  
    return f;  
           ^  
```  
  
The above was obtained with clang-14 and the `-std=c++20` flag. Since `std::result_of` was removed in C++20: Are there any plans to upgrade Boost.fiber to a more recent standard? The docs are still stating that C++11 is a requirement.

---

## Comment 1

> Username: j-stephan  
> Created at: 2022-05-10 12:52:54 UTC  
> Url: https://github.com/boostorg/fiber/issues/297#issuecomment-1122352025  

Nevermind, I just discovered #296.
