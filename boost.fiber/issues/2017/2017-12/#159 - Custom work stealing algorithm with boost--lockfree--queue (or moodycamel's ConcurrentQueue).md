# #159 - Custom work stealing algorithm with boost::lockfree::queue (or moodycamel's ConcurrentQueue) [Closed]

> Username: brandon-kohn  
> Created at: 2017-12-16 14:55:03 UTC  
> Updated at: 2017-12-17 09:47:52 UTC  
> Closed at: 2017-12-17 09:47:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/159  

Hi :)  
  
I've been playing around with the idea of modifying the work stealing algorithm by making a single change which swaps out the default rqueue_ type to either boost's lockfree queue or moodycamel's ConcurrentQueue. In either case I change the steal implementation to pop and then requeue if the context retrieved is pinned.  I'm then trying the skynet example you have with these modified work stealing algorithms. Neither works. My assumption is that the algorithm is relying on the blocking nature of the spinlock queue, but it's not clear to me why that would be the case. I was hoping you might have some insights on why a lock-free queue might be the wrong choice for this algorithm.

---

## Comment 1

> Username: olk  
> Created at: 2017-12-17 09:47:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/159#issuecomment-352243717  

boost.fiber does not requrire blocking queues - you have to take care about the happend before/after of detaching and attaching a fiber context to a scheduler. boost.fiber contains a proof of concept spmc queue using weak memory models.
