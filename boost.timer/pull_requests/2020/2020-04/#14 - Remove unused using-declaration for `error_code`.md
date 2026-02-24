# #14 Remove unused using-declaration for `error_code` [Merged]

> Username: tcm-marcel  
> Created at: 2020-04-24 14:14:08 UTC  
> Updated at: 2020-04-27 09:45:11 UTC  
> Merged at: 2020-04-24 21:04:54 UTC  
> Closed at: 2020-04-24 21:04:54 UTC  
> Url: https://github.com/boostorg/timer/pull/14  

_Sorry for not following the official contributing process, I thought this small change might not worth it. Feel free to close if unwanted._   
  
`boost::system::error_code` is included in the namespace, even though it is not even used in this file.   
  
The problem I encounter because of this: When configuring `boost/chrono` to be header-only, the `error_code` symbol is not included anymore and the `cpu_timer.cpp` doesn't compile.

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-04-24 21:04:46 UTC  
> Url: https://github.com/boostorg/timer/pull/14#issuecomment-619238209  

> Sorry for not following the official contributing process  
  
This is the official contributing process now because it runs CI. :-) Thanks.

---

## Comment 2

> Username: tcm-marcel  
> Created_at: 2020-04-27 09:30:52 UTC  
> Url: https://github.com/boostorg/timer/pull/14#issuecomment-619856797  

Great, thank you!  
Let me say that this is amazing, I was never able contribute super small changes like this in an project so fast!

---
