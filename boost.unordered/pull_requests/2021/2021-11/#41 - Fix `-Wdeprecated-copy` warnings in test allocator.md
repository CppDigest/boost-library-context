# #41 Fix `-Wdeprecated-copy` warnings in test allocator [Merged]

> Username: cmazakas  
> Created at: 2021-11-19 19:11:43 UTC  
> Updated at: 2021-11-23 20:54:27 UTC  
> Merged at: 2021-11-23 20:34:08 UTC  
> Closed at: 2021-11-23 20:34:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/41  

Closes #34

---

## Comment 1

> Username: cmazakas  
> Created_at: 2021-11-23 20:17:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/41#issuecomment-977128525  

Alright, @pdimov, the code is now move operation free and `default`s the assignment operators where supported.

---
