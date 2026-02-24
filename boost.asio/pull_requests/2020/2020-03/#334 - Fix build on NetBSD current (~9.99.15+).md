# #334 Fix build on NetBSD current (~9.99.15+) [Closed]

> Username: stixpjr  
> Created at: 2020-03-27 05:24:13 UTC  
> Updated at: 2020-12-30 01:19:54 UTC  
> Closed at: 2020-12-30 01:19:54 UTC  
> Url: https://github.com/boostorg/asio/pull/334  

The kevent udata member was switched from intptr_t to void*, to synchromize the struct field format with other BSDs and Darwin:  
https://github.com/NetBSD/src/commit/10682b98ea88784ce2876630e77babf09b053228

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:19:54 UTC  
> Url: https://github.com/boostorg/asio/pull/334#issuecomment-752294539  

This was fixed in asio 1.16.1 / boost 1.73.

---
