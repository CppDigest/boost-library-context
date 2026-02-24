# #1357 - Handle operations being abandoned in websocket::stream [Closed]

> Username: djarek  
> Created at: 2018-12-07 20:31:36 UTC  
> Updated at: 2019-02-28 02:42:18 UTC  
> Closed at: 2019-02-28 02:42:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1357  

If a currently running I/O operation is abandoned, paused operations in websocket::stream may cause a memory leak.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-29 17:21:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1357#issuecomment-450507342  

Test: https://github.com/boostorg/beast/pull/1358

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-01-29 01:14:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1357#issuecomment-458367597  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-01-29 01:23:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1357#issuecomment-458369773  

To be honest I"m not sure if this is a bug or not

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-02-28 02:23:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1357#issuecomment-468110752  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-28 02:42:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1357#issuecomment-468114541  

Resolved in boostorg#1358
