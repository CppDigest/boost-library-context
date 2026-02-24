# #16 Correct Posix complaince standards [Closed]

> Username: ya1gaurav  
> Created at: 2015-01-29 12:02:22 UTC  
> Updated at: 2016-11-13 19:44:51 UTC  
> Closed at: 2016-11-13 19:44:51 UTC  
> Url: https://github.com/boostorg/interprocess/pull/16  

sem_timedwait() & pthread_mutex_timedlock() are added under IEEE Std 1003.1-2001  
_POSIX_TIMEOUTS should be checked to correct Posix Compliance IEEE standard.  
Refer : http://man7.org/linux/man-pages/man7/feature_test_macros.7.html

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-11-13 19:44:51 UTC  
> Url: https://github.com/boostorg/interprocess/pull/16#issuecomment-260207461  

Posix detection was rewritten long time ago in commit:  
  
https://github.com/boostorg/interprocess/commit/25bb48435f94ce108547b26c03b47d7425254ddf  
  
Thanks for the report.

---
