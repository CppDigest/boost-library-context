# #327 - Suspended scheduling algorithms (work_stealing or shared_work) not notified when work is scheduled from another thread [Open]

> Username: beojan  
> Created at: 2024-11-06 18:06:09 UTC  
> Updated at: 2024-11-06 18:06:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/327  

I have a pool of worker threads and I want to schedule fibers onto this pool from the main thread, without running any of these worker fibers on the main thread itself. I'm doing this using the `shared_work` (or `work_stealing`) algorithm.  
  
If I set the `suspend` option to `true` to prevent the worker threads busy-waiting when they have no work the worker fibers never run because the algorithms on the worker threads aren't notified when work is pushed onto the queue from the main thread.
