# #141 Make CMakeLists.txt work with a system installations of libcxx. [Closed]

> Username: camio  
> Created at: 2015-06-18 20:12:40 UTC  
> Updated at: 2015-06-28 10:25:44 UTC  
> Closed at: 2015-06-28 10:25:44 UTC  
> Url: https://github.com/boostorg/hana/pull/141  

The proper way to link using clang and a system install of libc++ is  
to use the flag '-stdlib=libc++' and the library flag '-lc++abi'.  
  
Fixes #135

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-06-28 10:25:44 UTC  
> Url: https://github.com/boostorg/hana/pull/141#issuecomment-116252381  

Closing this, since #135 is now fixed.

---
