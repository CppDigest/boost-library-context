# #23 Change example documentation for the multi-producer queue to indicate lock-freedom [Merged]

> Username: bazald  
> Created at: 2019-08-18 23:10:52 UTC  
> Updated at: 2020-04-29 15:55:17 UTC  
> Merged at: 2020-04-29 15:55:17 UTC  
> Closed at: 2020-04-29 15:55:17 UTC  
> Url: https://github.com/boostorg/atomic/pull/23  

Given my best understanding of lock-free and wait-free algorithms, the included multi-producer queue example is not entirely wait-free. The `pop` functionality is wait-free since it does not require any looping whatsoever. However, the `push` function implements a CAS loop, which is a telltale sign that it is merely lock-free. With arbitrarily many producer threads, it is theoretically possible for a producer thread to starve indefinitely in `push`. As no fixed bound on the number of iterations of the loop can given for `push` to have a guarantee of success, `push` is not wait-free.  
  
Given the logic above, I've modified the documentation to rename the section and the data structure to claim lock-freedom rather than wait-freedom while noting that the consumer's part is in fact wait-free, since that is clearly the goal of this design.

---
