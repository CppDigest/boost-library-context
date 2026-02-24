# #184 - Is there a way to preempt a running fiber? [Closed]

> Username: baiyongrui  
> Created at: 2018-10-06 03:37:42 UTC  
> Updated at: 2018-10-06 06:40:32 UTC  
> Closed at: 2018-10-06 06:40:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/184  

Hello, I was wondering is it possible to preempt a fiber which will run for a long time? Thanks in advance!

---

## Comment 1

> Username: olk  
> Created at: 2018-10-06 06:40:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/184#issuecomment-427550928  

boost.fiber uses cooperative preemption ... fibers preempt itself.  
you could implement some checkpoints (similar to checkpoints for interruption in boost.thread).
