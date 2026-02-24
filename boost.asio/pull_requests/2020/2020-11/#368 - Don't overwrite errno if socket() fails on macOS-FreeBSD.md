# #368 Don't overwrite errno if socket() fails on macOS/FreeBSD [Open]

> Username: nomis  
> Created at: 2020-11-16 20:07:19 UTC  
> Updated at: 2020-11-16 20:07:19 UTC  
> Url: https://github.com/boostorg/asio/pull/368  

The unconditional call to setsockopt() even if socket() fails is breaking some of my unit tests for socket() errors because errno is overwritten with EBADF.  
  
Fixes: 42b575ce2cca (Access errno only when on the error path.)

---
