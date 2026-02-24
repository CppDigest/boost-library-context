# #67 Add support for Windows error code ERROR_NOT_SUPPORTED [Merged]

> Username: Lastique  
> Created at: 2021-05-28 19:52:52 UTC  
> Updated at: 2021-05-29 01:25:40 UTC  
> Merged at: 2021-05-29 00:36:52 UTC  
> Closed at: 2021-05-29 00:36:52 UTC  
> Url: https://github.com/boostorg/system/pull/67  

The error code description reads as "The request is not supported," which is close to POSIX `EOPNOTSUPP` "Operation not supported."

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-05-28 20:22:37 UTC  
> Url: https://github.com/boostorg/system/pull/67#issuecomment-850651880  

Here, `operation_not_supported` is what looked appropriate to me, but there is also `not_supported`, which looks similar. I'm not sure which is better, so if you think it should be changed, let me know.

---

## Comment 2

> Username: Lastique  
> Created_at: 2021-05-28 20:29:50 UTC  
> Url: https://github.com/boostorg/system/pull/67#issuecomment-850655014  

BTW, test failures seem unrelated to the change. Looks like clang is too old for libstdc++ version from gcc 11 as it doesn't support some of the compiler features used by the standard library.

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-05-28 21:58:25 UTC  
> Url: https://github.com/boostorg/system/pull/67#issuecomment-850695327  

I have changed to `not_supported` since that's what MSVC standard library [does](https://github.com/microsoft/STL/blob/3cafa97eecdbfde41ea5c09126f877a7eb97f9e9/stl/src/syserror.cpp#L61).

---
