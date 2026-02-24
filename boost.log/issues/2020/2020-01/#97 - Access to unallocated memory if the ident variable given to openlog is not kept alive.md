# #97 - [syslog_backend] Access to unallocated memory if the ident variable given to openlog is not kept alive. [Closed]

> Username: joudinet  
> Created at: 2020-01-22 15:22:05 UTC  
> Updated at: 2020-01-22 18:41:00 UTC  
> Closed at: 2020-01-22 18:40:39 UTC  
> Url: https://github.com/boostorg/log/issues/97  

Most libc implementations do not copy the ident argument of openlog(). Only musl does it. This is a known issue for glibc:  
https://sourceware.org/bugzilla/show_bug.cgi?id=25442  
So it is the caller's responsibility to keep this string alive until the last call to syslog(). Here, I think this code should keep a copy of the ident variable if it is not empty, before calling openlog:  
https://github.com/boostorg/log/blob/b395be29c24791d40cfd16662d8966895626c150/src/syslog_backend.cpp#L140  
  
Another possibility is to force every libc implementations to make a deep copy of the ident argument of openlog (impracticable in my opinion, as it is not even required in the POSIX description).  
A third possibility would be to document the ident parameter on syslog_backend to let the user know they are responsible for it.  
  
What do you think?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-01-22 15:42:01 UTC  
> Updated at: 2020-01-22 15:43:38 UTC  
> Url: https://github.com/boostorg/log/issues/97#issuecomment-577247045  

I think it is worth working around in Boost.Log, for immediate practical reasons.  
  
Though POSIX doesn't require a deep copy, it also doesn't say that the caller is supposed to keep the string alive. However, by default it is assumed that function arguments can be discarded after the call. I think fixing `openlog` implementations is reasonable and worth pushing for, but it will not happen at once.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-01-22 18:41:00 UTC  
> Url: https://github.com/boostorg/log/issues/97#issuecomment-577326663  

Thanks for the report.
