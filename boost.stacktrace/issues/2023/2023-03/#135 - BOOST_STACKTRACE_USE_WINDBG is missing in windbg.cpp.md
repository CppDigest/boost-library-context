# #135 - BOOST_STACKTRACE_USE_WINDBG is missing in windbg.cpp [Closed]

> Username: deadlocklogic  
> Created at: 2023-03-29 16:34:56 UTC  
> Updated at: 2024-09-12 08:17:43 UTC  
> Closed at: 2024-09-12 08:17:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/135  

https://github.com/boostorg/stacktrace/blob/95065ca63883c0b1d6754bdb7bb106058c7532cb/src/windbg.cpp#L1-L10 is missing:  
```cpp  
#define BOOST_STACKTRACE_USE_WINDBG   
```

---

## Comment 1

> Username: deadlocklogic  
> Created at: 2023-03-29 17:02:26 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/135#issuecomment-1488974484  

Just another issue:  
CMake doesn't take into consideration: `BOOST_STACKTRACE_LINK` flag, so the the `.cpp` file are compiled anyway which leads to `multiple definition` error on `MINGW`.

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-09-12 08:17:33 UTC  
> Updated at: 2024-09-12 08:17:43 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/135#issuecomment-2345573495  

Not sure that `#define BOOST_STACKTRACE_USE_WINDBG` as there are some cygwin usages that use libunwind interfaces. Looks like it works as is.  
  
As for the second issue - please provide a PR for it, I can not reproduce it.
