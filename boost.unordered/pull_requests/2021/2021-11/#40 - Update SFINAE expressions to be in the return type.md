# #40 Update SFINAE expressions to be in the return type [Merged]

> Username: cmazakas  
> Created at: 2021-11-18 20:55:28 UTC  
> Updated at: 2021-11-19 16:15:30 UTC  
> Merged at: 2021-11-19 02:32:06 UTC  
> Closed at: 2021-11-19 02:32:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/40  

Closes #39 and overrides https://github.com/boostorg/unordered/pull/8

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-11-19 01:55:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/40#issuecomment-973655835  

Looks like a healthy mix of `boost::enable_if` and `boost::enable_if_c`.  I'd probably pick one (and that would be `boost::enable_if_` from TT).

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-11-19 02:31:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/40#issuecomment-973671872  

Good enough for government use.

---
