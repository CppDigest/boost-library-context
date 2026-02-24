# #309 - Multithreading issue - descriptor data not guarded correctly. [Closed]

> Username: tradingapps-service  
> Created at: 2019-04-30 06:44:27 UTC  
> Updated at: 2020-12-30 01:12:07 UTC  
> Closed at: 2020-12-30 01:12:06 UTC  
> Url: https://github.com/boostorg/asio/issues/309  

Hi - We are using boost 1.51.0 asio library on linux and seeing crash at times. Upon investigation we noticed that it is crashing when it tries to access a pointer which is already set to 0.  
  
function where it crashes - asio::detail::epoll_reactor::start_op  
It crashes while accessing if (descriptor_data->shutdown_) line. gdb shows descriptor_data is already set to 0.  
  
While debugging i noticed that epoll_reactor::deregister_descriptor might be setting the object to zero.  
  
Question - the mutex variable of descriptor_data object is used to guard access of this object. However this variable is marked as 0 in epoll_reactor::deregister_descriptor function, while the thread is holding the mutex_. In a different thread executing start_op function and waiting on this function will find null object when the mutex lock is freed.  
  
Shouldn't there be a check in asio::detail::epoll_reactor::start_op function, after the mutex lock is grabbed to see descriptor_data is still valid object and then access it's shutdown variable.?  
  
Also, isn't it a bad practice of marking the object as 0 but still holding the lock via it's member variable.?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:12:05 UTC  
> Url: https://github.com/boostorg/asio/issues/309#issuecomment-752293306  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#736](https://github.com/chriskohlhoff/asio/issues/736).
