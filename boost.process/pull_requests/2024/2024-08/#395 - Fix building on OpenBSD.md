# #395 Fix building on OpenBSD [Merged]

> Username: brad0  
> Created at: 2024-08-24 11:43:30 UTC  
> Updated at: 2024-10-24 23:00:59 UTC  
> Merged at: 2024-10-24 23:00:58 UTC  
> Closed at: 2024-10-24 23:00:58 UTC  
> Url: https://github.com/boostorg/process/pull/395  

OpenBSD does not have close_range() nor does NetBSD.  
  
OpenBSD needs environ like the other *BSD's.  
  
The build was erroring on kp_pid, it looks like p_pid is appropriate.  
  
Fixes: https://github.com/boostorg/process/issues/391

---

## Comment 1

> Username: ghost  
> Created_at: 2024-08-25 23:10:43 UTC  
> Updated_at: 2024-08-25 23:12:20 UTC  
> Url: https://github.com/boostorg/process/pull/395#issuecomment-2309032466  

Hey, in case you were wanting to add other features to OpenBSD which are missing such as `bp2::ext::exe` you can base your code loosely on this file, just like I did with my past contributions to boost process v2:  
  
https://github.com/time-killer-games/xproc/blob/main/process.cpp  
  
Look for the function `exe_from_proc_id()`.  
  
Be sure to change the license from MIT to what boost process v2 has, I give you permission as the copyright holder of the relevent code. Process Hacker developers and devKathy are only responsible for small portions of code specific to the Windows platform, the rest I am the copyright holder for, which means I wrote all OpenBSD-related code, (and *BSD-related code as a whole). Make sure if you use enough of my code to be considered substantial that I am mentioned in the copyright of the related files (refer to me as "Samuel Venable").  
  
I don't really have the time to add these features myself right now, but there is no obligation for you to do so either.

---

## Comment 2

> Username: ghost  
> Created_at: 2024-09-03 17:03:40 UTC  
> Url: https://github.com/boostorg/process/pull/395#issuecomment-2327022648  

Does this actually build now? If not, let me know once you solve all build errors. @klemens-morgenstern would like me to test this pull request once you are done.

---
