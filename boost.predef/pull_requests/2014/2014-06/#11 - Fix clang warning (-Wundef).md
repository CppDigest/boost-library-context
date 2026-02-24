# #11 Fix clang warning (-Wundef) [Closed]

> Username: ruslo  
> Created at: 2014-06-15 10:08:11 UTC  
> Updated at: 2014-07-09 14:49:41 UTC  
> Closed at: 2014-07-09 14:10:40 UTC  
> Url: https://github.com/boostorg/predef/pull/11  

Since macro BOOST_PREDEF_DETAIL_OS_DETECTED used before initialization it  
must be checked with `#if !defined(` command instead of `#if !(`.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2014-07-09 14:10:40 UTC  
> Url: https://github.com/boostorg/predef/pull/11#issuecomment-48475786  

Manually did the changes for this as I need pull request to get done on the develop branch, not the master branch. Thanks for finding the problem.

---

## Comment 2

> Username: ruslo  
> Created_at: 2014-07-09 14:49:06 UTC  
> Url: https://github.com/boostorg/predef/pull/11#issuecomment-48482540  

> I need pull request to get done on the develop branch  
  
Okay, got it) BTW you can mention it in the top level README

---
