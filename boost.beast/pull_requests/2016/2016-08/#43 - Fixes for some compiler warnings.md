# #43 Fixes for some compiler warnings [Closed]

> Username: tiesjan  
> Created at: 2016-08-02 11:48:12 UTC  
> Updated at: 2016-08-04 15:33:55 UTC  
> Closed at: 2016-08-03 19:54:14 UTC  
> Url: https://github.com/boostorg/beast/pull/43  

Hi there!  
  
I've started using Beast in my own project and while compiling I noticed a few compiler warnings that were easy to fix. These commits fix the following warnings:  
  
```  
include/beast/websocket/detail/frame.hpp:113:10: warning: comparison is always true due to limited range of data type [-Wtype-limits]  
     if(v >= 0 && v <= 999)  
        ~~^~~~  
  
include/beast/websocket/teardown.hpp:54:2: warning: extra ‘;’ [-Wpedantic]  
 };  
```  
  
Please let me know what you think.  
  
And of course: thanks for all the great work so far, I love how Beast seamlessly integrates in my Asio-based application! :smile:

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-08-02 12:02:49 UTC  
> Url: https://github.com/boostorg/beast/pull/43#issuecomment-236883705  

[![Coverage Status](https://coveralls.io/builds/7267793/badge)](https://coveralls.io/builds/7267793)  
  
Coverage remained the same at 98.067% when pulling **e5497257cd67a1badfb885fddf09063d38bb7e5d on Hefting:master** into **8204d9524eab498c08cb2dd994d74237ce4b9db3 on vinniefalco:master**.

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-08-02 12:03:13 UTC  
> Url: https://github.com/boostorg/beast/pull/43#issuecomment-236883775  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/43?src=pr) is 98.06% (diff: 100%)  
  
> Merging [#43](https://codecov.io/gh/vinniefalco/Beast/pull/43?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
``` diff  
@@             master        #43   diff @@  
==========================================  
  Files            71         71            
  Lines          4036       4036            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           3958       3958            
  Misses           78         78            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [8204d95...e549725](https://codecov.io/gh/vinniefalco/Beast/compare/8204d9524eab498c08cb2dd994d74237ce4b9db3...e5497257cd67a1badfb885fddf09063d38bb7e5d?src=pr)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-08-03 19:52:51 UTC  
> Url: https://github.com/boostorg/beast/pull/43#issuecomment-237351686  

@Hefting Glad to hear that Beast is useful to you! These fixes look great, I'll incorporate them into b10. Thanks!

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2016-08-03 19:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/43#issuecomment-237352056  

These fixes have been incorporated into #44

---

## Comment 5

> Username: tiesjan  
> Created_at: 2016-08-04 15:33:55 UTC  
> Url: https://github.com/boostorg/beast/pull/43#issuecomment-237590865  

Great, good to hear it was useful!

---
