# #156 - Many examples are using their own barrier.hpp [Closed]

> Username: ldionne  
> Created at: 2017-11-15 21:55:35 UTC  
> Updated at: 2017-11-17 09:06:44 UTC  
> Closed at: 2017-11-16 07:17:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/156  

It seems like the library is also providing the same `barrier` class. Should the examples be using `<boost/fiber/barrier.hpp>`, or is there a subtle reason for them not to? As a novice trying to get his program to work, I found that confusing as I don't know which one to use (and whether that may be the cause for my program not working).

---

## Comment 1

> Username: ldionne  
> Created at: 2017-11-15 22:28:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/156#issuecomment-344750304  

Ah! I just changed `::barrier` to `boost::fibers::barrier` in the work stealing example and it starts segfaulting at the end of the program. So I guess there _is_ a difference.

---

## Comment 2

> Username: olk  
> Created at: 2017-11-16 07:17:20 UTC  
> Updated at: 2017-11-16 07:18:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/156#issuecomment-344836623  

examples work_sharing and work_stealing using barrier in order to synchronize the worker-threads  (structures inside the sharing/stealing-algorithm)

---

## Comment 3

> Username: ldionne  
> Created at: 2017-11-17 08:52:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/156#issuecomment-345182576  

So, the only difference is that one of them is using `boost::fibers::condition_variable` and the other one is using `std::condition_variable`, right? Would it be possible to rename the one used in examples to `thread_barrier` or something like this, then? This is a trap waiting to bite any novice that's copy/pasting examples when experimenting (such as myself).

---

## Comment 4

> Username: olk  
> Created at: 2017-11-17 09:06:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/156#issuecomment-345185643  

done
