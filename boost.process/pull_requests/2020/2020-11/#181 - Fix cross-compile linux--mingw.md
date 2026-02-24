# #181 Fix cross-compile linux->mingw [Merged]

> Username: orgads  
> Created at: 2020-11-04 09:12:03 UTC  
> Updated at: 2021-07-05 06:48:58 UTC  
> Merged at: 2021-07-05 06:48:02 UTC  
> Closed at: 2021-07-05 06:48:03 UTC  
> Url: https://github.com/boostorg/process/pull/181  

Headers are lower case.

---

## Comment 1

> Username: Martchus  
> Created_at: 2021-06-12 18:40:09 UTC  
> Url: https://github.com/boostorg/process/pull/181#issuecomment-860092758  

I've just ran into this problem as well. It would be great if the patch could be merged. I can confirm that it works.

---

## Comment 2

> Username: xantares  
> Created_at: 2021-07-05 06:14:45 UTC  
> Url: https://github.com/boostorg/process/pull/181#issuecomment-873833443  

+1 cc @klemens-morgenstern

---

## Comment 3

> Username: orgads  
> Created_at: 2021-07-05 06:20:04 UTC  
> Url: https://github.com/boostorg/process/pull/181#issuecomment-873836122  

Rebased.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2021-07-05 06:25:35 UTC  
> Url: https://github.com/boostorg/process/pull/181#issuecomment-873838862  

Has this been tested on windows for native compiles?

---

## Comment 5

> Username: orgads  
> Created_at: 2021-07-05 06:26:18 UTC  
> Url: https://github.com/boostorg/process/pull/181#issuecomment-873839196  

Yes

---

## Comment 6

> Username: xantares  
> Created_at: 2021-07-05 06:28:28 UTC  
> Url: https://github.com/boostorg/process/pull/181#issuecomment-873840324  

on native windows the filesystem is case-insensitive so either works but not on linux

---
