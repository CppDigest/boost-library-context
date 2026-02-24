# #81 Make round_robin::suspend_until() always set asio timer. [Merged]

> Username: nat-goodspeed  
> Created at: 2016-04-29 13:23:56 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Merged at: 2016-04-29 17:15:27 UTC  
> Closed at: 2016-04-29 17:15:27 UTC  
> Url: https://github.com/boostorg/fiber/pull/81  

This gives notify() something to cancel if need be. It has bothered me that if the dispatcher calls sched_algorithm::suspend_until( time_point::max()), and therefore suspend_timer_ is not set and no async_wait() call is outstanding, notify() cannot have any effect.  
  
I very much doubt steady_timer::expires_at() is implemented using std::condition_variable::wait_until(), so I don't believe we should have a problem calling expires_at() with time_point::max().  
  
Truthfully, with the lambda loop, I'm not sure under what circumstances the dispatcher will _ever_ call suspend_until(), though.  
  
Avoid resetting the timer to the same abs_time. Since setting an asio timer cancels any outstanding async_wait() call -- apparently even if the new abs_time is the same as the previous -- I am concerned about possibly spinning.  
  
Use add_service() instead of use_service() since we now forbid multiple threads from using the same io_service instance. Give nested service class a virtual destructor since asio promises to eventually delete the pointer as pointer-to-base-class.

---
