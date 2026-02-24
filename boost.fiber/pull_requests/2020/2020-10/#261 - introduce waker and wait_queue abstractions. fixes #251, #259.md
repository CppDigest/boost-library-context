# #261 introduce waker and wait_queue abstractions. fixes #251, #259 [Merged]

> Username: dmitryikh  
> Created at: 2020-10-14 14:22:29 UTC  
> Updated at: 2020-10-14 14:46:35 UTC  
> Merged at: 2020-10-14 14:36:19 UTC  
> Closed at: 2020-10-14 14:36:19 UTC  
> Url: https://github.com/boostorg/fiber/pull/261  

introduce waker and wait_queue abstractions. fixes #251, #259  
  
```  
* remove context::wait_hook_ and context::twstatus in flavor to waker_epoch_ and waker class  
* this avoids data races in case of wait_until() operations, when the context  
  could be timeouted and rescheduled on the other OS thread. In this case could  
  be data races with context::wait_hook_ and inconsistences context::twstatus  
  states.  
* using context::waker_epoch_ introduces mechanism when the old wakers become  
  outdated and waker::wake() is just no op. This fixes data races explained in  
  the previous point  
* fibers waiting queue with timeouts and notification mechanisms are incapsulated into  
  wait_queue class. This introduce simple abstraction level to be used in  
  different synchronization primitives  
* the same wait_queue is used in context::wait_queue_ to synchronize fiber::join() operations  
```  
  
I know there is a huge diff!   
But this PR could be good for simplification and improved stability (correctness) of boost::fiber library.  
Let's discuss it!

---

## Comment 1

> Username: olk  
> Created_at: 2020-10-14 14:36:29 UTC  
> Url: https://github.com/boostorg/fiber/pull/261#issuecomment-708446038  

verfy fine - ty Dmitry

---

## Comment 2

> Username: dmitryikh  
> Created_at: 2020-10-14 14:42:49 UTC  
> Url: https://github.com/boostorg/fiber/pull/261#issuecomment-708450029  

@olk, I don't expected that the PR will be so fast adopted. I expected there should be code review. But I don't familiar with boost.org rules..

---

## Comment 3

> Username: olk  
> Created_at: 2020-10-14 14:46:35 UTC  
> Url: https://github.com/boostorg/fiber/pull/261#issuecomment-708452287  

I've trust you - unfortunately I'm very busy and I've no time (but I looked at the changes in the PR).

---
