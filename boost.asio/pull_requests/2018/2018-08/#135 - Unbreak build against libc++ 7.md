# #135 Unbreak build against libc++ 7 [Closed]

> Username: jbeich  
> Created at: 2018-08-06 14:10:16 UTC  
> Updated at: 2018-11-12 20:01:23 UTC  
> Closed at: 2018-11-12 20:01:22 UTC  
> Url: https://github.com/boostorg/asio/pull/135  

libc++ >= 7 [lacks](https://reviews.llvm.org/rL324290) `<experimental/string_view>` while libc++ < 4 [may not](https://reviews.llvm.org/rL276238) have `<string_view>`. Found [downstream](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=230401).

---

## Comment 1

> Username: jbeich  
> Created_at: 2018-11-12 20:01:22 UTC  
> Url: https://github.com/boostorg/asio/pull/135#issuecomment-438010354  

Obsolete after 43874d549741.

---
