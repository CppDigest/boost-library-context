# #177 - check_sanity() scoped mutex locked after assertion [Closed]

> Username: lioriz  
> Created at: 2022-06-12 22:44:32 UTC  
> Updated at: 2022-07-16 16:20:18 UTC  
> Closed at: 2022-07-16 14:07:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/177  

In [check_sanity()](https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp#L636) function,  
In the scenario where multiple processes share the same ```shared memory```, if the ```shared memory``` is corrupted for some reason then when one of the processes calls ```check_sanity()``` the [scoped mutex](https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp#L639) in the function locks but the process [crashes with assertion](https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp#L648) without unlocking the mutex, causing any other process that will call ```check_sanity()``` to be stuck on the locked ```scoped mutex```.  
  
If there is a ```mutex``` in a ```shared memory``` object it must be unlocked before ```assertion```, ```exception```, or ```return```.  
I would expect the ```check_sanity()``` function to return ```false``` in the case of corrupted memory instead of ```assert```.

---

## Comment 1

> Username: lioriz  
> Created at: 2022-06-13 13:03:38 UTC  
> Updated at: 2022-06-20 14:03:33 UTC  
> Url: https://github.com/boostorg/interprocess/issues/177#issuecomment-1153887869  

opened [PR 178](https://github.com/boostorg/interprocess/pull/178) for this issue.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2022-07-16 14:07:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/177#issuecomment-1186193633  

Many thanks for the PR!

---

## Comment 3

> Username: lioriz  
> Created at: 2022-07-16 16:20:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/177#issuecomment-1186230940  

> Many thanks for the PR!  
  
My pleasure.
