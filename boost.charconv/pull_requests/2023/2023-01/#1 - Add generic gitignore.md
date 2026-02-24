# #1 Add generic gitignore [Closed]

> Username: mborland  
> Created at: 2023-01-09 18:39:42 UTC  
> Updated at: 2023-08-15 19:11:48 UTC  
> Closed at: 2023-08-15 19:11:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/1  

[ci skip]

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-01-09 18:43:15 UTC  
> Url: https://github.com/boostorg/charconv/pull/1#issuecomment-1376112722  

These seem mostly wrong. `build/` is definitely wrong - it's a legitimate directory containing the Jamfile required to build the library and is (and should remain) under version control. Much of the rest seem only to be relevant if you CMake with the binary directory equal to the source directory, which you should never do.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-01-09 18:50:43 UTC  
> Url: https://github.com/boostorg/charconv/pull/1#issuecomment-1376124737  

Is there a reason to have this library not header only? I just pulled from Boost.Math which is header only.

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-01-09 19:04:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/1#issuecomment-1376138886  

Yes. The floating point algorithms are a lot of code and there's no reason to have all that in headers.

---
