# #78 Fix integer overflow UB in test suite [Merged]

> Username: cmazakas  
> Created at: 2022-01-11 16:06:17 UTC  
> Updated at: 2022-01-11 19:39:29 UTC  
> Merged at: 2022-01-11 19:11:09 UTC  
> Closed at: 2022-01-11 19:11:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/78  

Enable the CI to run with `address-sanitizer=norecover undefined-sanitizer=norecover`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-01-11 17:24:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/78#issuecomment-1010190957  

`static_cast<unsigned>(x)` please.

---
