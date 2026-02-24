# #505 - Conditionally build SSL targets with bjam [Closed]

> Username: vinniefalco  
> Created at: 2017-06-17 20:16:57 UTC  
> Updated at: 2018-06-05 03:11:41 UTC  
> Closed at: 2018-06-05 03:11:41 UTC  
> Url: https://github.com/boostorg/beast/issues/505  

bjam for the ssl based examples doesn't work on Windows. We need a way to 1. reliably detect and configure OpenSSL if available, and 2. conditionally build these targets depending on whether OpenSSL was found.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:34 UTC  
> Url: https://github.com/boostorg/beast/issues/505#issuecomment-384022616  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-29 00:54:48 UTC  
> Url: https://github.com/boostorg/beast/issues/505#issuecomment-385216653  

@grafikrobot ?

---

## Comment 3

> Username: grafikrobot  
> Created at: 2018-04-29 02:07:18 UTC  
> Url: https://github.com/boostorg/beast/issues/505#issuecomment-385219901  

Hm, right.. we would want an `openssl.jam` module in b2. Like we have zlib and other library detection modules.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-05-29 02:14:14 UTC  
> Url: https://github.com/boostorg/beast/issues/505#issuecomment-392633760  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-06-05 03:11:40 UTC  
> Url: https://github.com/boostorg/beast/issues/505#issuecomment-394567403  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
