# #252 - Writer degrades to busy polling after reconnection [Closed]

> Username: anarthal  
> Created at: 2025-05-18 17:08:31 UTC  
> Updated at: 2025-05-19 14:46:57 UTC  
> Closed at: 2025-05-19 14:46:57 UTC  
> Url: https://github.com/boostorg/redis/issues/252  

After a successful reconnection, the writer task revers to doing busy polling, rather than waiting for notifications, yielding in a high CPU usage. This is due to the following lines changing the expiry of the writer timer:  
  
https://github.com/boostorg/redis/blob/328ad97a79cf66108a11294462aa636b5abce927/include/boost/redis/connection.hpp#L445-L447  
  
Timers are stateful, and keep the set expiration time. When a wait is issued against a timer with an expiry date in the past, it completes immediately, hence yielding the aforementioned busy wait situation.  
  
I discovered this while implementing the tests for #250. The fix is trivial - using a separate timer for waits. This also simplifies the logic of #250. I will issue a PR shortly. I've opened the issue to keep track of it, in case anyone has experienced it.
