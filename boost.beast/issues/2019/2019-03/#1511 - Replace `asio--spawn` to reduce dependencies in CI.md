# #1511 - Replace `asio::spawn` to reduce dependencies in CI [Closed]

> Username: djarek  
> Created at: 2019-03-07 17:38:03 UTC  
> Updated at: 2019-04-06 18:00:42 UTC  
> Closed at: 2019-04-06 18:00:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1511  

`asio::spawn` introduces quite a few dependencies, dropping it could help.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-04-06 17:45:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1511#issuecomment-480523522  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-04-06 18:00:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1511#issuecomment-480524628  

When I rewrite a test, I avoid the use of `spawn` so this will happen naturally given time.
