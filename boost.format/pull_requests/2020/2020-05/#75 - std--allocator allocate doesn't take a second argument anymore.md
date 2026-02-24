# #75 std::allocator allocate doesn't take a second argument anymore [Closed]

> Username: mgaunard  
> Created at: 2020-05-13 17:17:30 UTC  
> Updated at: 2020-05-26 02:36:59 UTC  
> Closed at: 2020-05-26 02:36:59 UTC  
> Url: https://github.com/boostorg/format/pull/75  

This was deprecated in C++17 and removed in C++20.  
As is the code does not compile with GCC 10.1 in C++20 mode.

---

## Comment 1

> Username: mgaunard  
> Created_at: 2020-05-13 18:08:17 UTC  
> Url: https://github.com/boostorg/format/pull/75#issuecomment-628157196  

I see the AppVeyor build failed on cygwin but it seems to be a problem with the build environment, not the code change.

---

## Comment 2

> Username: glenfe  
> Created_at: 2020-05-26 02:36:59 UTC  
> Url: https://github.com/boostorg/format/pull/75#issuecomment-633777381  

Resolved in 5884c3d8140cdcd2d1bef374fcc216244d5faaa5.

---
