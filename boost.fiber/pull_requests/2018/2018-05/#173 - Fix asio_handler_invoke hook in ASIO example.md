# #173 Fix asio_handler_invoke hook in ASIO example [Merged]

> Username: djarek  
> Created at: 2018-05-18 19:06:11 UTC  
> Updated at: 2018-05-19 14:38:48 UTC  
> Merged at: 2018-05-19 12:05:04 UTC  
> Closed at: 2018-05-19 12:05:04 UTC  
> Url: https://github.com/boostorg/fiber/pull/173  

The previous hook required the Fn to be CopyConstructible, wheras ASIO 1.66+ permits MoveConstructible-only CompletionHandlers.  
  
Resolves: https://github.com/boostorg/beast/issues/1134

---

## Review 1 [Approved]

> Username: vinniefalco  
> Created_at: 2018-05-18 21:39:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/fiber/pull/173#pullrequestreview-121580093  

Boost.Fiber requires C++11 so the forwarding reference is allowed

---

## Comment 2

> Username: olk  
> Created_at: 2018-05-19 12:05:12 UTC  
> Url: https://github.com/boostorg/fiber/pull/173#issuecomment-390400512  

ty

---
