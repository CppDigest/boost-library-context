# #213 - Segfaults when condition_variable_any uses context->wait_queue_ at the same time as the scheduler [Closed]

> Username: bgemmill  
> Created at: 2019-09-16 18:50:59 UTC  
> Updated at: 2019-09-16 20:27:43 UTC  
> Closed at: 2019-09-16 20:27:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/213  

Good afternoon,  
  
I've been tracking down some segfaults in my code, and it looks like double usage of the context's wait queue by the scheduler and condition_variable_any.  
  
For some background, there is a round-robin scheduler that uses context->wait_queue_ as an intrusive ready list as described [here](https://www.boost.org/doc/libs/1_71_0/libs/fiber/doc/html/fiber/scheduling.html#class_context).  
  
Elsewhere in the code, there's a function that's guarded by a boost::fibers::recursive_mutex and needs a condition variable. Since it's not the standard mutex, the condition variable needs to be the _any version.  
  
If we have code like this:  
```  
std::unique_lock<boost::fibers::recursive_mutex> guard(_mtx);  
_cv.wait_until(guard, some_timeout);  
```  
That code will segfault during the wakeup portion of `wait_until` if the timeout was reached.  
  
The flow to the segfault looks like this:  
1) The context's intrusive data is set [here](https://github.com/boostorg/fiber/blob/boost-1.71.0/include/boost/fiber/condition_variable.hpp#L109) by the condition variable.  
2) The condition variable goes to suspend the context [here](https://github.com/boostorg/fiber/blob/boost-1.71.0/include/boost/fiber/condition_variable.hpp#L114).  
3) And flow goes to the algo [here](https://github.com/boostorg/fiber/blob/boost-1.71.0/include/boost/fiber/algo/algorithm.hpp#L42) for some other work to run in the mean time.  
4) We wait for some_timeout to pass.  
5) The algo gets notification that the fiber is ready [here](https://github.com/boostorg/fiber/blob/boost-1.71.0/include/boost/fiber/algo/algorithm.hpp#L40), which will add it to the ready queue, causing the double write.  
6) Other work is done until that fiber is at the top of the ready queue  
7) The algo picks this fiber as ready [here](https://github.com/boostorg/fiber/blob/boost-1.71.0/include/boost/fiber/algo/algorithm.hpp#L42), removing it from the algo's ready queue and causing another write as it pops out of the queue. The scheduler then sets it to run.  
8) Segfault [here](https://github.com/boostorg/fiber/blob/boost-1.71.0/include/boost/fiber/condition_variable.hpp#L119) since the condition variable didn't know about the algo's ready queue.  
  
The first question is, am I using the library correctly? :-)  
  
Looking a bit more into condition_variable_any, the `wait` function does not appear to have the equivalent of `wait_until`'s step 8, it just looks like only [locks the mutex and continues](https://github.com/boostorg/fiber/blob/boost-1.71.0/include/boost/fiber/condition_variable.hpp#L81).  
  
Does condition_variable_any need to manipulate this queue if the algo is also doing it? The issue appears to go away if this block is [commented out](https://github.com/boostorg/fiber/blob/boost-1.71.0/include/boost/fiber/condition_variable.hpp#L116-L121).  
  
Thanks a lot for your time.

---

## Comment 1

> Username: bgemmill  
> Created at: 2019-09-16 20:27:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/213#issuecomment-531944492  

After a bit more digging, it looks like the problem is the scheduler is using the context's wait queue rather than the ready queue. Fixed on this end.
