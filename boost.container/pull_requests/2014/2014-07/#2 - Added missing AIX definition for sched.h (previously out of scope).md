# #2  Added missing AIX definition for sched.h (previously out of scope). [Merged]

> Username: ismirlian  
> Created at: 2014-07-16 20:52:37 UTC  
> Updated at: 2014-07-30 14:32:07 UTC  
> Merged at: 2014-07-30 14:32:07 UTC  
> Closed at: 2014-07-30 14:32:07 UTC  
> Url: https://github.com/boostorg/container/pull/2  

In mutex.hpp there was a compilation failure because, "'sched_yield' was not declared in this scope." As a result, I added the missing file (sched.h) in AIX to allow it to see the function.

---
