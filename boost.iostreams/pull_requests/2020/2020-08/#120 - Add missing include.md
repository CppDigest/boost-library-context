# #120 Add missing include [Closed]

> Username: camolezi  
> Created at: 2020-08-27 13:53:22 UTC  
> Updated at: 2020-09-16 18:44:01 UTC  
> Closed at: 2020-09-16 18:44:01 UTC  
> Url: https://github.com/boostorg/iostreams/pull/120  

This patch makes the header able to be built standalone, making possible C++ clang modules builds. @vgvassilev

---

## Comment 1

> Username: mclow  
> Created_at: 2020-08-27 14:04:03 UTC  
> Url: https://github.com/boostorg/iostreams/pull/120#issuecomment-681969802  

This is an odd header; I don't think this was ever meant to be build standalone.

---
