# #343 - Data race condition in ‘boost/thread/lock_types.hpp’ [Open]

> Username: dimitrov-ds  
> Created at: 2021-06-08 08:27:08 UTC  
> Updated at: 2021-06-08 08:27:08 UTC  
> Url: https://github.com/boostorg/thread/issues/343  

Hi,  
I've just run Coverity, version 2020.03, static analysis on a program using Boost v 1_75_0  
It has highlighted several potential issues one of which I will describe here.  
  
In lock_types.hpp the ‘unique_lock’ class contains a mutex and a Boolean guard, called ‘is_locked’, is checked, via the ‘owns_lock()’ method, prior to locking and unlocking the mutex and then set to true or false respectively. For example, on line 451 the mutex is unlocked and on line 452 the ‘is_locked’ is set to false. Another thread can at the same time call owns_lock() to check the locked status and potentially get wrong answer.  
This, to me and the Coverity static analysis, creates a data race condition. Is that a false positive in the static analysis tool? What am I missing?  
  
Thanks,  
Dimitar
