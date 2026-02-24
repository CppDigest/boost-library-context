# #97 Disable getentropy backend on Linux and Android [Merged]

> Username: Lastique  
> Created at: 2019-04-04 13:26:59 UTC  
> Updated at: 2019-04-19 10:44:27 UTC  
> Merged at: 2019-04-19 10:44:27 UTC  
> Closed at: 2019-04-19 10:44:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/97  

Since getentropy is implemented via getrandom in Linux kernel, getentropy  
cannot work if the kernel doesn't support getrandom. Given this, there is  
no reason to ever use getentropy on Linux.  
  
We used to allow this backend based on glibc version check, but there are  
configurations where a newer glibc is running with an older kernel, and  
getentropy always fails in runtime. Disabling getentropy in such cases  
allows to fall back to the generic POSIX backend.  
  
References https://github.com/boostorg/uuid/issues/92#issuecomment-479380506.  
  
This fixes #92

---
