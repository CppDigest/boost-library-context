# #96 Fix for clang on windows [Closed]

> Username: jzmaddock  
> Created at: 2016-03-27 18:27:35 UTC  
> Updated at: 2016-03-27 20:43:19 UTC  
> Closed at: 2016-03-27 19:02:11 UTC  
> Url: https://github.com/boostorg/test/pull/96  

When I try to build with clang on windows (4.2.1 Compatible Clang 3.9.0 (trunk), on top of VC14) I get an error complaining that accessing "this" within an __except block is unimplemented.  This patch just stores the value in a local variable to make it available with the __finally clause.  
  
Allows Boost.Test to be built with clang on windows, in VC compatibility mode.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2016-03-27 19:02:11 UTC  
> Url: https://github.com/boostorg/test/pull/96#issuecomment-202125954  

Applied to topic/PR96-clang-windows-fix, will be merged to develop in a couple of hours (internal CI).  
  
Thanks

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2016-03-27 20:43:19 UTC  
> Url: https://github.com/boostorg/test/pull/96#issuecomment-202142246  

Merged to develop

---
