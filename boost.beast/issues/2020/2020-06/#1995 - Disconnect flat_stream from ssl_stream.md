# #1995 - Disconnect flat_stream from ssl_stream [Closed]

> Username: vinniefalco  
> Created at: 2020-06-23 02:17:07 UTC  
> Updated at: 2024-05-31 13:15:53 UTC  
> Closed at: 2024-05-31 13:15:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1995  

Asio now linearises scatter/gather I/O in `ssl::stream`, so we can simplify our code. And probably deprecate `flat_stream` at some point:  
  
https://github.com/chriskohlhoff/asio/commit/17637a48ccbfa2f63941d8393a7c8316a8df4a79

---

## Comment 1

> Username: stale[bot]  
> Created at: 2020-07-25 03:51:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1995#issuecomment-663803536  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: ashtum  
> Created at: 2024-05-29 17:39:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1995#issuecomment-2137940251  

I think we can safely deprecate Beast's `flat_stream` and `ssl_stream`. Removing `ssl_stream` would break a lot of users' code, so I suggest we remove `flat_stream` and rewrite `ssl_stream` to be a facade of `asio::ssl::stream`, keeping it indefinitely. However, we will rewrite the examples to avoid using it and mark it as deprecated.
