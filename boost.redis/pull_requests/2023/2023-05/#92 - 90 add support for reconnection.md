# #92 90 add support for reconnection [Merged]

> Username: mzimbres  
> Created at: 2023-05-03 21:25:00 UTC  
> Updated at: 2023-05-13 16:28:00 UTC  
> Merged at: 2023-05-06 13:39:30 UTC  
> Closed at: 2023-05-06 13:39:30 UTC  
> Url: https://github.com/boostorg/redis/pull/92  

Climbing out of the rabbit hole ... six weeks later. This PR adds many high-level functionality to the connection classes like builtin resolve, connect, ssl-handshake, resp3-handshake, health-check, reconnection and a logger.  
  
Although Boost.Redis seems to have the edge in terms of performance, it was still leaving the features listed above as an exercise to the user, which is counter productive and only upset users, as can be seem in the issues.

---
