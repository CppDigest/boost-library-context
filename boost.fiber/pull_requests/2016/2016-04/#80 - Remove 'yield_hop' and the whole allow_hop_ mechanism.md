# #80 Remove 'yield_hop' and the whole allow_hop_ mechanism. [Merged]

> Username: nat-goodspeed  
> Created at: 2016-04-06 16:27:06 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Merged at: 2016-04-06 17:03:13 UTC  
> Closed at: 2016-04-06 17:03:13 UTC  
> Url: https://github.com/boostorg/fiber/pull/80  

Given the necessity for fibers::asio::round_robin to share its ready queue  
among all threads calling io_service::run() on the same io_service instance,  
the capability to allow hop (or not) in the fibers::asio::yield mechanism is  
redundant.  
  
I shouldn't have put this on top of the autoecho change, but I don't know git well enough to untangle them.

---
