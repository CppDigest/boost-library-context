# #242 CI: Limit single test execution time to 30 seconds [Merged]

> Username: Kojoley  
> Created at: 2018-10-05 17:11:51 UTC  
> Updated at: 2018-10-08 19:28:21 UTC  
> Merged at: 2018-10-08 18:34:21 UTC  
> Closed at: 2018-10-08 18:34:21 UTC  
> Url: https://github.com/boostorg/thread/pull/242  

It should help identify hanging tests that push CI over build time limit.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-10-06 02:18:37 UTC  
> Url: https://github.com/boostorg/thread/pull/242#issuecomment-427538682  

The timeouts on travis could be avoided either by fixing `execution_monitor` (like this b5743ca4d1746a60a7c4b43d6cff118b4f2f5201) or by increasing timeout, the choice is yours.

---

## Comment 2

> Username: viboes  
> Created_at: 2018-10-07 16:56:50 UTC  
> Url: https://github.com/boostorg/thread/pull/242#issuecomment-427668530  

IIUC the -l30, this will just prevent from spending the maximum time allowed, and limit each test to 30s, isn't it?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-10-07 17:00:44 UTC  
> Url: https://github.com/boostorg/thread/pull/242#issuecomment-427668862  

Yes, it is. Did I worded the title badly?  
As you can see, Appveyor did not hit the hour limit with this and you can find which tests hang (or too slow).

---
