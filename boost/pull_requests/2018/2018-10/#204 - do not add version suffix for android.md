# #204 do not add version suffix for android [Closed]

> Username: huangqinjin  
> Created at: 2018-10-11 13:30:24 UTC  
> Updated at: 2023-04-04 11:58:06 UTC  
> Closed at: 2018-10-12 02:34:42 UTC  
> Url: https://github.com/boostorg/boost/pull/204  

Android studio could automatically pack shared libraries into final apk. But if we link to `libboost_system.so`, which is a soft link to `libboost_system.so.1.68.0`, only the former will be packed.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-10-11 13:57:56 UTC  
> Url: https://github.com/boostorg/boost/pull/204#issuecomment-428965655  

This looks reasonable, but we don't take pull requests into `master`. Changes get put into `develop`, then tested, and then merged to `master` later.

---

## Comment 2

> Username: huangqinjin  
> Created_at: 2018-10-12 02:28:47 UTC  
> Url: https://github.com/boostorg/boost/pull/204#issuecomment-429183295  

@mclow #206

---

## Comment 3

> Username: mclow  
> Created_at: 2018-10-12 02:34:42 UTC  
> Url: https://github.com/boostorg/boost/pull/204#issuecomment-429184255  

Closing this since we've got #206 now.

---
