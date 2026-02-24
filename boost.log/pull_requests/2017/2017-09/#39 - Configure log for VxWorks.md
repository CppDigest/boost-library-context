# #39 Configure log for VxWorks [Merged]

> Username: kuhlenough  
> Created at: 2017-09-07 02:05:32 UTC  
> Updated at: 2018-06-01 11:19:16 UTC  
> Merged at: 2018-06-01 11:11:51 UTC  
> Closed at: 2018-06-01 11:11:51 UTC  
> Url: https://github.com/boostorg/log/pull/39  

IPC and Default factories use APIs VX does not have

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-07 08:22:05 UTC  
> Url: https://github.com/boostorg/log/pull/39#issuecomment-327727235  

What are the missing APIs? I'm especially interested about the default factories.

---

## Comment 2

> Username: kuhlenough  
> Created_at: 2017-09-10 00:47:17 UTC  
> Url: https://github.com/boostorg/log/pull/39#issuecomment-328312321  

I'm investigating, and will reopen soon with an improved request

---

## Comment 3

> Username: kuhlenough  
> Created_at: 2017-09-12 01:11:09 UTC  
> Url: https://github.com/boostorg/log/pull/39#issuecomment-328701897  

Here is a better pull with excellent test results. Still a couple IPC tests throwing odd exceptions, but they look related to other libraries other than log.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-09-12 10:19:57 UTC  
> Url: https://github.com/boostorg/log/pull/39#issuecomment-328809671  

The object name scopes are intended to be non-overlapping. For example, the same object name in the global and process group scopes should identify different objects. This changes with this PR.  
  
If there isn't any replacement to the POSIX functions on VxWorks, I think we should use the closest reasonable replacement, but with a distinct prefix. For instance, we could assume that all processes running under the same user belong to the same session and process group. Thus for `object_name::process_group` the prefix should be "pgid." + `getuid()` and for `object_name::session` it should be "sid." + `getuid()`. If the necessary API appears in the future we can replace `getuid` with it rather easily.

---

## Comment 5

> Username: kuhlenough  
> Created_at: 2017-09-13 03:29:49 UTC  
> Url: https://github.com/boostorg/log/pull/39#issuecomment-329048647  

That sounds like a reasonable approach.  I'll update the request.

---

## Review 6 [Changes requested]

> Username: Lastique  
> Created_at: 2018-04-26 00:20:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/log/pull/39#pullrequestreview-115396328  

📁 src/dump.cpp

```diff
 195 |- #if defined(__i386__) && (defined(__PIC__) || defined(__PIE__)) && !(defined(__clang__) || (defined(BOOST_GCC) && BOOST_GCC >= 50100))
 195 |+ #if defined(__i386__) && (defined(__PIC__) || defined(__PIE__)) && !(defined(__clang__) || (defined(BOOST_GCC) && BOOST_GCC >= 50100)) \
 196 |+ 	||( defined(__VXWORKS__) && defined(__PIC__))
```

> Username: Lastique  
> Created_at: 2018-04-26 00:14:07 UTC  
> Updated_at: 2018-04-30 00:57:01 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r184241113  

Is this change necessary? `__PIC__` is tested  before, and I believe the compiler is some version of gcc, so it should be covered as well, shouldn't it?

> Username: kuhlenough  
> Created_at: 2018-04-26 00:34:28 UTC  
> Updated_at: 2018-04-30 00:57:01 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r184243554  

I'll double check, but I think it was failure in GCC 4.x 64bit IA compile that threw an undefined opcode on the other branch.

> Username: kuhlenough  
> Created_at: 2018-04-29 05:17:07 UTC  
> Updated_at: 2018-04-30 00:57:01 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r184871524  

Yes, that's it. __i386__ is the problem, the GCC 4.8.1.10 -m64 -mcmodel=large -mno-red-zone -fno-omit-frame-pointer -fno-defer-pop -fPIC -O3 that VxWorks uses is reserving %ebx   
  
would you prefer ?  
  
#if (defined(__i386__) || defined(__VXWORKS__)) && (defined(__PIC__) || defined(__PIE__)) && !(defined(__clang__) || (defined(BOOST_GCC) && BOOST_GCC >= 50100))

> Username: Lastique  
> Created_at: 2018-04-29 09:35:57 UTC  
> Updated_at: 2018-04-30 00:57:01 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r184877116  

Yes, and a comment that VxWorks reserves ebx even in 64-bit mode would be useful. Thanks.

> Username: kuhlenough  
> Created_at: 2018-05-02 15:32:00 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r185539632  

Done, please see the update pull request.


📁 example/wide_char/main.cpp

```diff
  82 |     );
  83 | 
  84 |+ #if !defined(BOOST_LOG_NO_LOCALE)
```

> Username: Lastique  
> Created_at: 2018-04-26 00:16:47 UTC  
> Updated_at: 2018-04-30 00:57:01 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r184241453  

This example is part of documentation, I really don't want to pollute the examples with preprocessor checks. I'd rather leave this file as is, even if it fails to compile, and recommend patching Boost.Locale.

> Username: kuhlenough  
> Created_at: 2018-04-26 00:52:36 UTC  
> Updated_at: 2018-04-30 00:57:01 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r184245513  

VxWorks locale support is pretty basic, timezone support, not much more.  Supporting boost.locale is long way off. So why don't I just skip building the example for Vxworks in the Jamfile instead? That way we don't stall the test suite.

> Username: Lastique  
> Created_at: 2018-04-26 09:37:47 UTC  
> Updated_at: 2018-04-30 00:57:01 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r184328271  

Boost.Locale can use other libraries like iconv or icu to implement some of its functionality. But disabling the example in the Jamfile is fine with me.


📁 include/boost/log/detail/config.hpp

```diff
 120 |+ #   define BOOST_LOG_NO_GETPGRP 
 121 |+ #   define BOOST_LOG_NO_GETSID 
 122 |+ #   define BOOST_LOG_NO_GETPWUID_R
```

> Username: Lastique  
> Created_at: 2018-04-26 00:19:51 UTC  
> Updated_at: 2018-04-30 00:57:01 UTC  
> Url: https://github.com/boostorg/log/pull/39#discussion_r184241801  

`BOOST_LOG_NO_GETPWUID_R` is also defined in src/posix/object_name.cpp. The definition should should better be in a single place.


---

## Comment 7

> Username: Lastique  
> Created_at: 2018-06-01 11:19:16 UTC  
> Url: https://github.com/boostorg/log/pull/39#issuecomment-393851151  

Merged to develop, thanks. Sorry for the delay.

---
