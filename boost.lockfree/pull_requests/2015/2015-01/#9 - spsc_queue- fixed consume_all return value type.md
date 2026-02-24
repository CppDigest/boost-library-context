# #9 spsc_queue: fixed consume_all return value type [Merged]

> Username: ephraimo  
> Created at: 2015-01-22 12:15:33 UTC  
> Updated at: 2015-01-25 08:14:36 UTC  
> Merged at: 2015-01-23 05:09:52 UTC  
> Closed at: 2015-01-23 05:09:52 UTC  
> Url: https://github.com/boostorg/lockfree/pull/9  

When using the compile_time_sized_ringbuffer version of spsc_queue::consume_all, the value returned is a bool (similar to the way consume_one works) instead of the number of elements consumed (similar to the way the runtime_sized_ringbuffer version works).  
  
This also generates the following warning in VS2012:  
boost/lockfree/spsc_queue.hpp(473): warning C4800: 'boost::lockfree::detail::ringbuffer_base<T>::size_t' : forcing value to bool 'true' or 'false' (performance warning)

---

## Comment 1

> Username: timblechmann  
> Created_at: 2015-01-23 05:09:55 UTC  
> Url: https://github.com/boostorg/lockfree/pull/9#issuecomment-71148568  

thanks a lot!

---
