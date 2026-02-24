# #243 execution_monitor::use_mutex sleeps the whole timeout duration [Merged]

> Username: Kojoley  
> Created at: 2018-10-08 19:35:08 UTC  
> Updated at: 2018-10-09 10:13:06 UTC  
> Merged at: 2018-10-09 04:25:41 UTC  
> Closed at: 2018-10-09 04:25:41 UTC  
> Url: https://github.com/boostorg/thread/pull/243  

Instead of sleeping the whole timeout duration, sleep for 500ms in a loop and check every iteration if the job was already done.

---

## Comment 1

> Username: viboes  
> Created_at: 2018-10-09 04:26:32 UTC  
> Url: https://github.com/boostorg/thread/pull/243#issuecomment-428055464  

I was not aware this code could be something that could delay the execution.  
Thanks for the PR

---
