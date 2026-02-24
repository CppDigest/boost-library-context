# #324 - thread_pool always hangs when used with steady_timer [Closed]

> Username: gummif  
> Created at: 2020-02-04 11:26:38 UTC  
> Updated at: 2020-12-30 01:12:55 UTC  
> Closed at: 2020-12-30 01:12:54 UTC  
> Url: https://github.com/boostorg/asio/issues/324  

On Windows, `thread_pool` always hangs when used as an executor for `steady_timer`  
  
```c++  
#include <boost/asio/steady_timer.hpp>  
#include <boost/asio/thread_pool.hpp>  
int main()  
{  
    boost::asio::thread_pool tp{1};  
    boost::asio::steady_timer t1{tp.get_executor()};  
} // hangs in destructor of thread_pool (in shutdown())  
```  
  
Cancelling the timer or setting expire an date seems to have no effect. Looks like the same issue as in https://github.com/chriskohlhoff/asio/issues/431.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:12:53 UTC  
> Url: https://github.com/boostorg/asio/issues/324#issuecomment-752293415  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#746](https://github.com/chriskohlhoff/asio/issues/746).
