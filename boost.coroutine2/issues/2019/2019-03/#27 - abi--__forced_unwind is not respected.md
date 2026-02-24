# #27 - abi::__forced_unwind is not respected [Closed]

> Username: justinasvd  
> Created at: 2019-03-13 20:21:59 UTC  
> Updated at: 2019-03-17 14:50:05 UTC  
> Closed at: 2019-03-17 14:50:05 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/27  

Consider an I/O bound coroutine as in the example below. This example crashes because Boost.Coroutine2 does not rethrow `abi::__forced_unwind`, which was raised by `pthread_cancel`.  
  
I have a patch for this problem for coroutines, but some additional support is also needed by the context lib here.  
  
```C++  
#include <thread>  
#include <iostream>  
  
#include <boost/coroutine2/all.hpp>  
  
using coroutine_t = boost::coroutines2::coroutine<int>;  
  
static void io_runner()  
{  
    auto coro = coroutine_t::pull_type([](coroutine_t::push_type &yield) {  
        do  
        {  
            yield(rand());  
            sleep(1);  
        } while (1);  
    });  
  
    for (int num : coro)  
        std::cout << num << std::endl;  
}  
  
int main()  
{  
    std::thread io_thread(io_runner);  
  
    sleep(10);  
    pthread_cancel(io_thread.native_handle());  
  
    io_thread.join();  
    return 0;  
}  
```
