# #69 Properly obtain the FreeBSD version (BOOST_OS_BSD_FREE) [Merged]

> Username: jeking3  
> Created at: 2017-11-09 17:29:16 UTC  
> Updated at: 2017-11-10 13:04:26 UTC  
> Merged at: 2017-11-10 13:04:26 UTC  
> Closed at: 2017-11-10 13:04:26 UTC  
> Url: https://github.com/boostorg/predef/pull/69  

Based on analysis of `<sys/param.h>` in file history for FreeBSD, the current detection logic is wrong.  
https://github.com/freebsd/freebsd/blob/master/sys/sys/param.h

---
