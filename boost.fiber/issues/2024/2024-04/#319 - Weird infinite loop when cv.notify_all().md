# #319 - Weird infinite loop when cv.notify_all() [Closed]

> Username: HardLaugh  
> Created at: 2024-04-19 01:35:21 UTC  
> Updated at: 2024-05-25 05:53:32 UTC  
> Closed at: 2024-05-25 05:53:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/319  

First of all, this is a very powerful library, regret having not met earlier  
follow the tutorial, start multiple threads, use condition variables to wait for finish. a strange thing happened, when main thread signal(cv.notify_all()) terminate, there was a chance (It's about 100 repetitions, maybe once) of an infinite loop. some threads(not all) stuck on pick_next function, but has no work todo. change cv.wait(lk) to cv.wait(lk, pred), strange things disappear.Is there any possible explanation?

---

## Comment 1

> Username: HardLaugh  
> Created at: 2024-04-19 01:44:13 UTC  
> Updated at: 2024-04-19 01:45:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/319#issuecomment-2065600083  

boost version: 1.82  
Synchronization: boost::fibers::mutex and boost::fibers::condition_variable

---

## Comment 2

> Username: HardLaugh  
> Created at: 2024-04-19 08:53:37 UTC  
> Updated at: 2024-04-19 09:23:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/319#issuecomment-2066129108  

One more question.In multi-thread scenarios, i think the exit should be synchronized with the main thread. Otherwise, some remote scheduler may have been destructed,  last worker try to remote notify(` heap-use-after-free detected` by `asan`),   
possible ways:  
1. thread_barriers  
2. main thread should hold lock to finish notify_all ??  
  
Under multi-thread stress testing, there are many problems with stable use. Is there a better official best practice?

---

## Comment 3

> Username: HardLaugh  
> Created at: 2024-05-25 05:53:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/319#issuecomment-2130827153  

done, I had to isolate a separate fiber environment, so I made a lot of the basic components myself
