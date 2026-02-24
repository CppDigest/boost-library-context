# #76 Do not include the whole Predef library [Merged]

> Username: Kojoley  
> Created at: 2018-12-26 21:09:00 UTC  
> Updated at: 2019-01-06 18:15:14 UTC  
> Merged at: 2019-01-06 18:14:44 UTC  
> Closed at: 2019-01-06 18:14:44 UTC  
> Url: https://github.com/boostorg/phoenix/pull/76  

Because of a single macro definition Phoenix includes the whole Predef library.  
  
Actually I am not sure what are the reasons to have `BOOST_PHOENIX_VERSION_NUMBER` macro. It was added by @fletchjp in a big commit 6a094019ee2050607cfaebd2b10834c59a7bd31e without a comment.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-12-30 23:34:51 UTC  
> Url: https://github.com/boostorg/phoenix/pull/76#issuecomment-450594321  

Closed and open to trigger CI

---

## Comment 2

> Username: djowel  
> Created_at: 2018-12-30 23:37:08 UTC  
> Url: https://github.com/boostorg/phoenix/pull/76#issuecomment-450594437  

Unless there's a significant justification for it. I'm for removal for the sake of minimizing dependency.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-01-05 19:10:52 UTC  
> Url: https://github.com/boostorg/phoenix/pull/76#issuecomment-451682604  

Ping

---
