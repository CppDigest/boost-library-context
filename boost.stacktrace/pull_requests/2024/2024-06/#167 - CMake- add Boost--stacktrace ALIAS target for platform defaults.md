# #167 CMake: add Boost::stacktrace ALIAS target for platform defaults [Merged]

> Username: leha-bot  
> Created at: 2024-06-14 22:29:24 UTC  
> Updated at: 2024-06-21 19:42:10 UTC  
> Merged at: 2024-06-20 08:26:28 UTC  
> Closed at: 2024-06-20 08:26:28 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167  



---

## Comment 1

> Username: coveralls  
> Created_at: 2024-06-14 22:58:01 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2168858249  

[![Coverage Status](https://coveralls.io/builds/68101688/badge)](https://coveralls.io/builds/68101688)  
  
coverage: 86.35%. remained the same  
when pulling **e512d7c8ce7cef78fb7316100e540355edc02010 on leha-bot:feat/cmake-alias-target**  
into **66c0f7a54fe8d47e3ecae40a7fe56d9042ec19ec on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2024-06-20 08:17:53 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2180093074  

[![Coverage Status](https://coveralls.io/builds/68192844/badge)](https://coveralls.io/builds/68192844)  
  
coverage: 86.35%. remained the same  
when pulling **30b472ba350c5fee8bcf9cccc1363d4d465fef7c on leha-bot:feat/cmake-alias-target**  
into **66c0f7a54fe8d47e3ecae40a7fe56d9042ec19ec on boostorg:develop**.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-06-20 15:15:20 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2180955162  

This change breaks the CMake build: https://github.com/boostorg/boost/actions/runs/9594246969/job/26456395308

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-06-20 15:17:23 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2180959280  

Instead of  
```  
add_library(Boost::stacktrace ALIAS Boost::stacktrace_windbg)  
```  
use  
```  
add_library(Boost::stacktrace ALIAS boost_stacktrace_windbg)  
```  
and similarly for the other two.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-06-20 15:18:07 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2180960750  

Also, you need to add CMake jobs to your CI.

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-06-21 11:31:20 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2182574869  

Can this please be fixed? It's breaking my boostorg/cmake CI and interfering with my work.

---

## Comment 7

> Username: leha-bot  
> Created_at: 2024-06-21 11:41:01 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2182589230  

Hello, yes, sorry; hotfix will be ready in no time.  
I actually want to ping you to ask about proper `boost_install()` usage, as I also want to expose it from Boost-config.cmake

---

## Comment 8

> Username: leha-bot  
> Created_at: 2024-06-21 11:55:23 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2182609563  

https://github.com/boostorg/stacktrace/pull/168  
  
@pdimov , may you show the example with CMake CI? I will contribute it also in another PR, thanks in advance.

---

## Comment 9

> Username: pdimov  
> Created_at: 2024-06-21 12:06:26 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2182625610  

> I actually want to ping you to ask about proper `boost_install()` usage, as I also want to expose it from Boost-config.cmake  
  
This might not be easy because `boost_install()` has never been tested with ALIAS targets and probably doesn't work as-is.

---

## Comment 10

> Username: pdimov  
> Created_at: 2024-06-21 12:09:49 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2182630769  

The Timer CI may be the best starting point  
  
https://github.com/boostorg/timer/blob/develop/.github/workflows/ci.yml#L268-L601  
  
because it's a compiled library (although macos-11 would need to be replaced with macos-14 because macos-11 is going away.)

---

## Comment 11

> Username: leha-bot  
> Created_at: 2024-06-21 12:29:12 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2182660403  

Yes, thanks, will try to play on my own fork with both of them (boost_install() and CI), maybe will   come up with some ideas about it. 👍

---

## Comment 12

> Username: pdimov  
> Created_at: 2024-06-21 13:25:53 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2182752045  

For the `boost_install()` part, maybe the way to do it is to declare `boost_stacktrace` as an INTERFACE library that links to the corresponding `boost_stacktrace_...` one. (`Boost::stacktrace` will be an ALIAS to `boost_stacktrace` as usual.)

---

## Comment 13

> Username: pdimov  
> Created_at: 2024-06-21 15:36:34 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2182983981  

I'm working on adding CI. Will look into the boost_stacktrace target next.

---

## Comment 14

> Username: pdimov  
> Created_at: 2024-06-21 17:02:19 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2183118119  

Added CMake CI to develop.

---

## Comment 15

> Username: pdimov  
> Created_at: 2024-06-21 18:17:33 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2183223494  

Added boost_stacktrace on a branch, but there are weird failures on macos-12 and macos-14. I'm not sure why they happen and what I need to do about them.  
  
macos-12 is just this  
```  
/Users/runner/work/stacktrace/boost-root/libs/stacktrace/test/test.cpp(279): test 'boost::stacktrace::stacktrace(1, 1).size() == 1' failed in function 'int main()'  
1 error detected.  
```  
(https://github.com/boostorg/stacktrace/actions/runs/9617263707/job/26528655980)  
whereas macos-14 is more convoluted and probably caused by inlining (https://github.com/boostorg/stacktrace/actions/runs/9617263707/job/26528657050).

---

## Comment 16

> Username: pdimov  
> Created_at: 2024-06-21 19:42:09 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/167#issuecomment-2183351862  

Added the Boost::stacktrace library (now properly installed and tested) to develop.

---
