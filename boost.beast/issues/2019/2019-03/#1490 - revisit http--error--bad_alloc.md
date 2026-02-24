# #1490 - revisit http::error::bad_alloc [Closed]

> Username: vinniefalco  
> Created at: 2019-03-01 21:36:23 UTC  
> Updated at: 2022-06-16 16:39:01 UTC  
> Closed at: 2022-06-16 16:39:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1490  

I dont think catching bad_alloc and turning it into an error_code is a great idea

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-03-31 22:15:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1490#issuecomment-478390441  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: djarek  
> Created at: 2019-05-29 20:47:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1490#issuecomment-497102898  

This error code is no longer used: https://github.com/boostorg/beast/search?l=C%2B%2B&q=bad_alloc

---

## Comment 3

> Username: Bak-Jin-Hyeong  
> Created at: 2019-09-18 00:56:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1490#issuecomment-532473001  

What about adding `field_limit` like `header_limit` or `body_limit`?  
  
Catching `bad_alloc` in code that calls `io_context::run` makes it difficult to limit memory for certain `basic_parser` instance only.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-09-18 01:30:14 UTC  
> Updated at: 2019-09-18 01:30:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1490#issuecomment-532479411  

There is no `field_limit`...  Under what situation do you think `bad_alloc` can come up? It should no longer be possible. If you can create a test that causes `bad_alloc` for the fields, I'd like to see it.
