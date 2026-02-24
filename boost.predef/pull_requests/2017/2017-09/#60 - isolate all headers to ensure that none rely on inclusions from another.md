# #60 isolate all headers to ensure that none rely on inclusions from another [Merged]

> Username: jeking3  
> Created at: 2017-09-27 23:41:47 UTC  
> Updated at: 2017-10-04 20:11:42 UTC  
> Merged at: 2017-10-04 15:20:46 UTC  
> Closed at: 2017-10-04 15:20:46 UTC  
> Url: https://github.com/boostorg/predef/pull/60  

This uses a bjam technique from winapi that @lastique came up with.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-04 13:53:43 UTC  
> Url: https://github.com/boostorg/predef/pull/60#issuecomment-334162546  

@grafikrobot this is ready for review.  Passed CI.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2017-10-04 18:13:45 UTC  
> Url: https://github.com/boostorg/predef/pull/60#issuecomment-334243492  

Seems there's something wrong with the logic.. https://travis-ci.org/boostorg/predef/jobs/283242438#L918

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-04 18:26:18 UTC  
> Updated_at: 2017-10-04 18:36:21 UTC  
> Url: https://github.com/boostorg/predef/pull/60#issuecomment-334247118  

It looks like the terms "unix" and "linux" are evaluating to 1, so instead of getting the string we're getting the evaluated term.  Interesting!

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-10-04 19:05:31 UTC  
> Url: https://github.com/boostorg/predef/pull/60#issuecomment-334258281  

I'm going to recommend you revert this pull request.  Based on how it is working with the preprocessor, if I fix just those two with special case logic it'll break on a bunch more systems in the field.  I don't see a way to make this logic work with generically named header files like "unix.h" where unix is a preprocessor definition.  Maybe something with BOOST_PP could make this work, but I'm not familiar with it.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-10-04 20:11:42 UTC  
> Url: https://github.com/boostorg/predef/pull/60#issuecomment-334275021  

It's actually fascinating to see that clang-4.0 and gcc-7.2.0 behave differently from previous compiler versions when expanding the macros that made that work... that or they no longer define "unix" or "linux" without the underscores...

---
