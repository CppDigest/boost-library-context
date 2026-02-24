# #230 Guard MSVC pragmas from clang-cl [Closed]

> Username: pdimov  
> Created at: 2021-02-28 02:08:24 UTC  
> Updated at: 2021-03-02 00:11:20 UTC  
> Closed at: 2021-03-02 00:11:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/230  

This patch avoids the "unknown pragma" warnings from Clang-cl (Clang for Windows, b2 toolset=clang-win.)

---

## Comment 1

> Username: robertramey  
> Created_at: 2021-03-02 00:11:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/230#issuecomment-788419385  

slightly modified this to be easier to maintain.  checked it in and now testing.

---
