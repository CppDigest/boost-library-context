# #311 - Launching fiber on a different thread [Closed]

> Username: beojan  
> Created at: 2023-11-22 22:53:55 UTC  
> Updated at: 2023-11-23 17:29:29 UTC  
> Closed at: 2023-11-23 06:33:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/311  

I'd like to launch fibers on a pool of worker threads, but not the main thread. Currently, I'm using `shared_work` scheduler in all the threads, launching fibers with `launch::post` and simply not calling `this_thread::yield` from the main thread in the hope that the fibers will just get scheduled on one of the workers (which are waiting on a condition variable). However, it seems with this setup my fibers never run at all.

---

## Comment 1

> Username: beojan  
> Created at: 2023-11-22 23:00:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/311#issuecomment-1823607187  

Turns out I was actually trying to launch from a thread where I hadn't set the scheduler. Nevertheless, it would be nice to be able to launch fibers on other threads.

---

## Comment 2

> Username: olk  
> Created at: 2023-11-23 06:33:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/311#issuecomment-1823875999  

Unfortunately you always need a scheduler, it is not possible to use fibers without a scheduler.

---

## Comment 3

> Username: beojan  
> Created at: 2023-11-23 17:29:29 UTC  
> Url: https://github.com/boostorg/fiber/issues/311#issuecomment-1824760021  

I'm not looking to launch fibers without a scheduler, I'm looking to launch  
fibers on a thread other than the one I'm launching from.  
  
On Wed, Nov 22, 2023, 22:34 Oliver Kowalke ***@***.***> wrote:  
  
> Closed #311 <https://github.com/boostorg/fiber/issues/311> as completed.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/311#event-11043673625>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA4OENJGPTNSBNTXGT22XT3YF3U5TAVCNFSM6AAAAAA7W5ZAIGVHI2DSMVQWIX3LMV45UABCJFZXG5LFIV3GK3TUJZXXI2LGNFRWC5DJN5XDWMJRGA2DGNRXGM3DENI>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>
