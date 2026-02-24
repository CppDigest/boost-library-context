# #17 optimize the fetch_ready. Break early after got ready fiber and reduce t... [Closed]

> Username: absolute8511  
> Created at: 2014-08-07 12:32:15 UTC  
> Updated at: 2016-06-06 11:36:32 UTC  
> Closed at: 2014-08-17 18:30:15 UTC  
> Url: https://github.com/boostorg/fiber/pull/17  

Improve the performance when there are lots of fibers created. The original code will have some overhead on the clock_type::now(). Also, since the schedule algorithm need only one fiber to get running, the "move_to" function can break early to improve the performance.

---

## Comment 1

> Username: olk  
> Created_at: 2014-08-17 18:30:15 UTC  
> Url: https://github.com/boostorg/fiber/pull/17#issuecomment-52430404  

thx, I've adapted your suggestion - please note that development takes place in branch 'develop'.

---
