# #39 Add debugapi.hpp [Merged]

> Username: vinniefalco  
> Created at: 2017-05-23 05:51:15 UTC  
> Updated at: 2017-05-25 19:02:53 UTC  
> Merged at: 2017-05-25 19:02:42 UTC  
> Closed at: 2017-05-25 19:02:42 UTC  
> Url: https://github.com/boostorg/winapi/pull/39  

This is from the original `<debugapi.h`> windows header, I'm not sure how this translates into Boost.WinAPI speak:  
  
```  
#if WINAPI_FAMILY_PARTITION(WINAPI_PARTITION_APP | WINAPI_PARTITION_SYSTEM)  
```

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-05-23 05:51:57 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-303297250  

Please wait until I sign off before merging - feel free to review though :)

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-05-23 09:48:41 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-303348341  

All the APIs in Boost.WinAPI provide Boost-friendly declarations in the `boost::detail::winapi` namespace. The declarations in the global namespace are not supposed to be used outside Boost.WinAPI itself.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-05-23 09:49:44 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-303348627  

Also, what is the reason to include `debugapi.h`?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-05-23 12:38:39 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-303384578  

I just followed the example in this file, which looked the most similar:  
https://github.com/boostorg/winapi/blob/develop/include/boost/detail/winapi/dbghelp.hpp

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-05-23 12:44:17 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-303385993  

I've updated the branch based on your feedback

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-05-23 14:53:07 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-303423709  

1. Please, don't provide `boost::detail::winapi::IsDebuggerPresent` if the API is not available.  
2. It would be better to do `using ::OutputDebugStringA` instead of the inline wrapper function. The wrapper functions are implemented only when `using`-declaration is not enough (mostly, when argument casts are required). Same for `OutputDebugStringW`.  
3. It would be nice to also provide `boost::detail::winapi::output_debug_string` inline function which calls `::OutputDebugStringA` or `::OutputDebugStringW` depending on the argument type.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-05-23 15:29:27 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-303435755  

I'll make those changes. *But*, by not providing `IsDebuggerPresent` it just pushes the ugly version macro checking on to all callers. Are you sure that's what we want? Or maybe I'm missing something?

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-05-23 17:03:36 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-303467444  

That's the current convention with the other APIs. The user's code has to be aware of what Windows versions it targets and take steps, if necessary, to work around any missing optional (from the user's code perspective) APIs.  
  
The point of Boost.WinAPI is not to re-implement Windows API where not present, but mostly avoiding inclusion of windows.h and workarounds for different Windows SDK bugs and quirks (read MinGW & co.) Boost.WinAPI also allows to configure the target Windows version.

---

## Comment 9

> Username: Lastique  
> Created_at: 2017-05-25 19:02:53 UTC  
> Url: https://github.com/boostorg/winapi/pull/39#issuecomment-304095363  

Thanks.

---
