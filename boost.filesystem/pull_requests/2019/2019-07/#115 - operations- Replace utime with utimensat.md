# #115 operations: Replace utime with utimensat [Closed]

> Username: neheb  
> Created at: 2019-07-31 04:23:31 UTC  
> Updated at: 2020-01-26 04:59:44 UTC  
> Closed at: 2019-07-31 13:35:33 UTC  
> Url: https://github.com/boostorg/filesystem/pull/115  

utime has been deprecated in POSIX 2008. It is optionally unavailable with  
uClibc-ng. That includes the header.

---
