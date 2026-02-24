# #21 optimize the fiber scheduler [Closed]

> Username: absolute8511  
> Created at: 2014-09-11 10:08:31 UTC  
> Updated at: 2016-06-06 11:36:32 UTC  
> Closed at: 2014-09-12 08:02:10 UTC  
> Url: https://github.com/boostorg/fiber/pull/21  

To avoid scanning the whole waiting queue each time checking the ready fibers, I added some variables to the worker fiber. While the fiber is changed to ready, the fiber in the queue will be moved to the head of the queue so that it can pass the check for ready. In this way, the fibers in the waiting queue always ordered by the wakeup time and the ready fibers always before unready fibers. So we can break early while scanning the waiting queue.

---
