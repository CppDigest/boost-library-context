# #1123 - Add "short read" explanation to documentation [Closed]

> Username: jzmaddock  
> Created at: 2018-05-10 16:35:17 UTC  
> Updated at: 2023-12-31 10:35:45 UTC  
> Closed at: 2023-12-31 10:35:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1123  

Beast: closing websocket stream throws "short read" exception  
  
Moved from https://svn.boost.org/trac10/ticket/13483

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-10 16:52:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1123#issuecomment-388114034  

In an ideal world, every client and server would gracefully end the SSL session by following the shutdown handshake procedure. Unfortunately, certain companies (cough, Google) can't be bothered with this polite behavior. There are many documented cases of programs which intentionally do not follow the SSL shutdown (such as Chrome). You will need to just ignore this error, or something similar.  
  
The documentation should explain this.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-11-28 21:43:33 UTC  
> Updated at: 2018-11-28 21:43:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1123#issuecomment-442615688  

This might be fixable actually: https://github.com/boostorg/beast/issues/38  
  
See also:  
https://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error

---

## Comment 3

> Username: ashtum  
> Created at: 2023-12-31 10:35:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1123#issuecomment-1872915923  

Addressed in https://github.com/boostorg/beast/commit/094f5ec5cb3be1c3ce2d985564f1f39e9bed74ff
