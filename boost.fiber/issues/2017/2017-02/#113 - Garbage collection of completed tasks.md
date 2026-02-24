# #113 - Garbage collection of completed tasks [Closed]

> Username: danichcr72  
> Created at: 2017-02-23 19:39:14 UTC  
> Updated at: 2017-04-30 19:19:43 UTC  
> Closed at: 2017-04-30 19:19:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/113  

Hi,  
  
  Another general issue that appeared in my experience with fiber is that completed tasks do not release their memory until the dispatcher/system fiber runs.  
  
  The dispatcher fiber is not scheduled in all (?) / most(?) schedulers unless there are no application fibers pending.  It would be great to provide another mechanism to garbage collect completed tasks, either by the user calling a function or by having the dispatcher fiber scheduled with some other priority.  
  
Thanks,  
  
Daniel Chavarria  
Trovares Inc.

---

## Comment 1

> Username: olk  
> Created at: 2017-02-24 09:01:54 UTC  
> Url: https://github.com/boostorg/fiber/issues/113#issuecomment-282240139  

One of the tasks of the dispatcher-fiber is to release terminated worker-fibers  (== fibers created by the user-code). The dispatcher-fiber is the first fiber in the ready-queue when the application/thread starts. It always enqueues itself before it hands-over to another worker-fiber (dequeued from the ready-queue).  
So the dispatcher-fiber should be executed multiple times in your app.

---

## Comment 2

> Username: danichcr72  
> Created at: 2017-02-24 16:46:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/113#issuecomment-282340764  

Thanks for the quick reply.  I can see that the dispatcher fiber is scheduled to run, however in the shared_work scheduler at least, it does not run unless there are no worker fibers pending:  
  
    if ( ! rqueue_.empty() ) { /*<  
            pop an item from the ready queue  
        >*/  
        ctx = rqueue_.front();  
        rqueue_.pop_front();  
        lk.unlock();  
        BOOST_ASSERT( nullptr != ctx);  
        context::active()->attach( ctx); /*<  
            attach context to current scheduler via the active fiber  
            of this thread  
        >*/  
    } else {  
        lk.unlock();  
        if ( ! lqueue_.empty() ) { /*<  
                nothing in the ready queue, return main or dispatcher fiber  
            >*/

---

## Comment 3

> Username: olk  
> Created at: 2017-02-24 17:13:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/113#issuecomment-282347629  

right - shared_work was previously in the examples , so I missed the implementation details

---

## Comment 4

> Username: olk  
> Created at: 2017-04-02 07:54:57 UTC  
> Updated at: 2017-04-02 07:55:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/113#issuecomment-290971015  

releated to #112

---

## Comment 5

> Username: olk  
> Created at: 2017-04-02 07:58:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/113#issuecomment-290971168  

The problem is that the dispatcher-fiber is not correctly scheduled in the shared_work algo.  
If you take a look at work_stealgin algo (branch develop) you see that the dipatcher-fiber is scheduled like in round_robin.  
I suggest to switch to work_stealing because it is faster (locality of data) than shared_work.  
Otherwise shared_work needs some counters that manage teh scheduling of main-/dispatcher-fiber ...

---

## Comment 6

> Username: olk  
> Created at: 2017-04-30 19:19:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/113#issuecomment-298251165  

use work-stealing scheduler instead
