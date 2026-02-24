# #163 - Is there a way to tell how many outstanding worker fibers exist? [Closed]

> Username: bgemmill  
> Created at: 2017-12-30 02:24:08 UTC  
> Updated at: 2022-09-18 15:25:50 UTC  
> Closed at: 2022-09-18 14:42:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/163  

Good evening, I'm trying to make a threaded work-stealing setup for an application I'm making, with the caveat that I won't know how many fibers will be made during the lifetime of the program, or when.  
  
The flow I'd like is:  
- Program start  
- Start worker threads  
- Start fibers (these may spawn additional fibers later, and greatly fluctuate in number over time)  
- Work stealing across threads  
- When all fibers exit, allow threads to exit  
- Exit program  
  
I'd like to prevent the worker threads from exiting until all work everywhere has completed, since new work may come up later on.  
  
I've been working through the examples here:  
http://www.boost.org/doc/libs/1_66_0/libs/fiber/doc/html/fiber/worker.html  
It looks like worker threads will exit when they have no work to do at any given moment, and need some sort of condition to prevent premature exit.  
  
Is there a way to catch fiber entry / exit so I can maintain a global counter and trigger worker thread exit only once there are no fibers remaining? I started looking at fiber properties to perhaps use one as RAII, but the context header seems to imply that object's lifetime isn't owned by the context itself.  
  
As a somewhat hacky workaround, I've been wrapping the functors passed to fibers with a separate RAII object, but this is fragile since any fiber created without said wrapper won't influence the count and could get lost.  
  
Is there a better way to go about this?

---

## Comment 1

> Username: olk  
> Created at: 2018-01-04 12:21:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/163#issuecomment-355269119  

- is there a way to catch fiber entry / exit  
no, you have to can increment/decrement a counter at entry/exit of a fiber-fucntion  
a context gets deallocated by the framework (decrementing the use-counter if fiber has been terminated)  
  
- it looks like worker threads will exit when they have no work to do at any given moment  
yes, to prevent work-trhead from terminating is to suspend the main-fiber (the fiber that represents the stack of the worker-thread)

---

## Comment 2

> Username: romange  
> Created at: 2018-01-11 16:04:15 UTC  
> Updated at: 2018-01-11 16:05:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/163#issuecomment-356973840  

@olk can cv.notify_one() possibly suspend the currently executing fiber?  
  
Consider the case where a huge incoming stream brings the workload to the system and for each item in the stream I need to open a fiber. Now, I would like to limit the number of concurrent fibers for this workload but if I block&increment the counter inside the fiber main function - it means the framework will create the unlimited number of fibers and they block will inside of their main function. This can be solved by pulling the wait&increment outside of fiber creation.   
  
However, in case of fiber termination, I can decrement at the end of the main function but not afterwards.   
If notify_one() suspends the fiber it means that  theoretically there can be unlimited number of suspended fibers that did not free their resources yet.

---

## Comment 3

> Username: olk  
> Created at: 2018-01-11 18:02:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/163#issuecomment-357010707  

`condition_variable::notify_one()` does not suspend the fiber  
  
if you receive the block of work items in one thread you can simple increment an integer - no need to block  
if you distribute the work items to several worker threads you could use an atomic integer

---

## Comment 4

> Username: romange  
> Created at: 2018-01-11 18:40:40 UTC  
> Updated at: 2018-01-11 18:43:07 UTC  
> Url: https://github.com/boostorg/fiber/issues/163#issuecomment-357021624  

I use a single thread for this example but I want to utilize it fully.  
  
Suppose I have an infinite stream of items that needs to be processed.  
Infinite - I can not hold them in memory at the same time.  
The processing of each item can potentially block upon I/O &network  
requests, therefore, in order to allow fully asynchronous behavior I call  
fibers::async(process_fn, item) for each item in the stream. I am not sure  
if there is a better approach to this.  
  
Obviously one needs to control the total number of active fibers so I use  
the integer together with condition_variable and mutex to implement a  
semaphore abstraction that controls resources: the producer fiber blocks via  
condition variable if number of active fibers grows beyond a certain limit.  
Once a worker fiber exits it decreases the variable and signals the  
producer that it can continue spawning a new fiber.  
  
Similarly in order to know if the processing fully finished (inside the  
main fiber) I can block on condition variable and wait until the counter  
goes to 0.  
  
So blocking on counter is intentional to throttle the producer fiber or to  
wait for fibers to finish.  Since as you said "cv.notify_one()" does not  
yield, this approach should work correctly because then it's guaranteed  
that the execution will switch only after the signaling fiber terminates.  
  
I would love to hear if there is a better (idiomatic) way with fibers to  
achieve fiber creation throttling or multiple-fibers-join.  
  
  
  
  
  
On Thu, Jan 11, 2018 at 8:02 PM, Oliver Kowalke <notifications@github.com>  
wrote:  
  
> condition_variable::notify_one() does not suspend the fiber  
>  
> if you receive the block of work items in one thread you can simple  
> increment an integer - no need to block  
> if you distribute the work items to several worker threads you could use  
> an atomic integer  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/163#issuecomment-357010707>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ADgSiL7gA_Lkcl1JiG0PfVIjbt5g4K7Zks5tJkzCgaJpZM4RPfOj>  
> .  
>  
  
  
  
--   
Best regards,  
     Roman

---

## Comment 5

> Username: bgemmill  
> Created at: 2018-01-11 19:11:06 UTC  
> Updated at: 2018-01-11 19:12:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/163#issuecomment-357030189  

@romange have the main fiber spin up workers, and have a counter protected by a mutex and condition variable... the main fiber should sleep on the CV. Workers poke the CV when they exit. When the main fiber wakes up from the CV, spin up more workers until the count gets over some amount and sleep again.  
  
I'm doing something similar to keep my worker threads alive by maintaining a global count of work.  
  
EDIT: being able to catch fiber entry / exit would make my life easier, but I'm getting by with a wrapper function.

---

## Comment 6

> Username: olk  
> Created at: 2018-01-11 19:22:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/163#issuecomment-357033266  

the API of boost.fiber is modelled after std::thread's API ... std::thread doesn't ahve etnry/exit   
how would you implement it with std::thread instead of using fiber? it might be that this solution could be applied to fibers too (with their sync. primitves).

---

## Comment 7

> Username: bgemmill  
> Created at: 2018-01-12 22:12:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/163#issuecomment-357368810  

@olk while I've usually used thread local destructors for this, similar to [here](https://stackoverflow.com/questions/24253584/when-is-a-thread-local-global-variable-initialized), it seems cleaner to have this in algo.  
  
For example, you could imagine `algo::awakened` could be passed a bool meaning "this one's new" and `algo::pick_next` passed a bool meaning "this one's never coming back".  
  
Alternately, you could imagine two new functions `entering` and `exiting` that could be overridden.

---

## Comment 8

> Username: bgemmill  
> Created at: 2022-09-18 15:25:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/163#issuecomment-1250331162  

@olk was there a change made to allow this or is starting up fibers in a counting wrapper still the best way forward?
