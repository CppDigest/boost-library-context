# #72 Add basic platform bits for Nuxi CloudABI. [Merged]

> Username: EdSchouten  
> Created at: 2015-08-27 14:28:31 UTC  
> Updated at: 2015-09-01 16:13:01 UTC  
> Merged at: 2015-09-01 16:13:01 UTC  
> Closed at: 2015-09-01 16:13:01 UTC  
> Url: https://github.com/boostorg/config/pull/72  

Nuxi CloudABI is a POSIX-like runtime environment purely built on the  
principle of capability-based security[1]. It allows you to run  
arbitrary untrusted binaries directly on top of a UNIX kernel without  
compromising system integrity.  
  
This change adds a basic platform configuration that defines a small set  
of options that allow it to build most of the Boost sources. The next  
step is to send out small fixes to individual libraries that don't build  
yet.  
  
[1] Nuxi CloudABI: https://github.com/NuxiNL/cloudlibc

---
