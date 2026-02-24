# #27 cmake: add missing -pthread to core-tests and websocket-tests [Closed]

> Username: cbodley  
> Created at: 2016-06-16 02:26:16 UTC  
> Updated at: 2017-07-25 17:44:16 UTC  
> Closed at: 2016-06-20 15:24:46 UTC  
> Url: https://github.com/boostorg/beast/pull/27  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-06-16 10:17:41 UTC  
> Url: https://github.com/boostorg/beast/pull/27#issuecomment-226445494  

Reviewers: @seelabs

---

## Comment 2

> Username: seelabs  
> Created_at: 2016-06-16 17:35:50 UTC  
> Url: https://github.com/boostorg/beast/pull/27#issuecomment-226557524  

:+1: Using the imported target seems to be the preferred over ${CMAKE_THREAD_LIBS_INIT}. On my system (ubuntu 16.04) they seem to do the same thing, though looking at https://github.com/Kitware/CMake/blob/master/Modules/FindThreads.cmake there are differences on other systems.  
  
Good change.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-06-16 17:36:54 UTC  
> Url: https://github.com/boostorg/beast/pull/27#issuecomment-226557814  

@seelabs Is there an easy way to address #28 in this PR?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2016-06-20 15:21:41 UTC  
> Url: https://github.com/boostorg/beast/pull/27#issuecomment-227174803  

This is a clear improvement and it passes review.  
:+1:

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-06-20 15:23:39 UTC  
> Url: https://github.com/boostorg/beast/pull/27#issuecomment-227175340  

@cbodley Thanks!

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2016-06-20 15:24:46 UTC  
> Url: https://github.com/boostorg/beast/pull/27#issuecomment-227175663  

This is now part of #29

---
