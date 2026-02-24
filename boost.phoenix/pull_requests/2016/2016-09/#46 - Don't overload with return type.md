# #46 Don't overload with return type. [Merged]

> Username: Flast  
> Created at: 2016-09-04 03:41:58 UTC  
> Updated at: 2016-09-04 03:42:15 UTC  
> Merged at: 2016-09-04 03:42:12 UTC  
> Closed at: 2016-09-04 03:42:12 UTC  
> Url: https://github.com/boostorg/phoenix/pull/46  

Some older GCC fails to overload.  
  
tested under  
- fedora rawhide gcc 6.1.1  
- fedora rawhide clang 3.8.0  
- fedora core 3 gcc 3.4.4  
- msvc 8 / 9sp1 / 10sp1 / 11u5 / 12u5 / 14u3

---
