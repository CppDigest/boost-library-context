# #158 Remove windows.h dependency. [Merged]

> Username: jzmaddock  
> Created at: 2021-10-05 18:55:36 UTC  
> Updated at: 2021-10-07 09:51:21 UTC  
> Merged at: 2021-10-06 10:10:18 UTC  
> Closed at: 2021-10-06 10:10:18 UTC  
> Url: https://github.com/boostorg/regex/pull/158  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-10-06 10:10:12 UTC  
> Url: https://github.com/boostorg/regex/pull/158#issuecomment-935900393  

Fixes https://github.com/boostorg/regex/issues/144  
Fixes https://github.com/boostorg/regex/issues/154

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2021-10-06 11:37:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/regex/pull/158#pullrequestreview-772539502  

📁 include/boost/regex/v5/w32_regex_traits.hpp

```diff
  81 | //
  65 |- typedef std::uint32_t lcid_type;   // placeholder for LCID.
  82 |+ typedef unsigned long lcid_type;        // placeholder for LCID.
```

> Username: Lastique  
> Created_at: 2021-10-06 11:31:02 UTC  
> Updated_at: 2021-10-06 11:37:02 UTC  
> Url: https://github.com/boostorg/regex/pull/158#discussion_r723149154  

This won't work the same with Cygwin, where `long` is 64-bit. You can use `boost::uint32_t`. Or check for `__LP64__`, like you do below.

> Username: jzmaddock  
> Created_at: 2021-10-06 12:49:21 UTC  
> Updated_at: 2021-10-06 12:49:22 UTC  
> Url: https://github.com/boostorg/regex/pull/158#discussion_r723211054  

Good catch, I might need to check the size of long or something, the issue is that this need to be *exactly* the same type as windows.h uses, otherwise there are errors when this header and windows.h are both included.  Same will be true with mingw/cygwin's flavour of windows.h.   I'll look into that shortly.

> Username: jzmaddock  
> Created_at: 2021-10-06 14:30:39 UTC  
> Url: https://github.com/boostorg/regex/pull/158#discussion_r723338602  

On second thoughts: use of windows.h is specifically disabled for non-MSVC builds, so this is a non-issue.

> Username: Lastique  
> Created_at: 2021-10-06 14:48:47 UTC  
> Updated_at: 2021-10-06 14:48:48 UTC  
> Url: https://github.com/boostorg/regex/pull/158#discussion_r723357377  

`windows.h` is certainly present on Cygwin, and users could be including it on their own. Besides, that's an ABI discrepancy - I'm not sure what will be in the upper bits when `lcid_type` is returned from a WinAPI function. I still consider this a problem.

> Username: jzmaddock  
> Created_at: 2021-10-07 08:03:30 UTC  
> Url: https://github.com/boostorg/regex/pull/158#discussion_r723942396  

>windows.h is certainly present on Cygwin, and users could be including it on their own. Besides, that's an ABI discrepancy - I'm not sure what will be in the upper bits when lcid_type is returned from a WinAPI function. I still consider this a problem.  
  
My point is that w32_regex_traits.hpp is an empty header (everything is #ifdef'ed out) for anything except MSVC, so there should never be any clash because regex won't be defining those symbols on Cygwin/Mingwin.

> Username: Lastique  
> Created_at: 2021-10-07 09:51:21 UTC  
> Url: https://github.com/boostorg/regex/pull/158#discussion_r724027398  

Oh, I see, you define `BOOST_REGEX_NO_W32` on Cygwin [here](https://github.com/boostorg/regex/blob/78bf0acd20c9c9fb96b7b53e46feb4880d1e9d35/include/boost/regex/config.hpp#L222).


---
