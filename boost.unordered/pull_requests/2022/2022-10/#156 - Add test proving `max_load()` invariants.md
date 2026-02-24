# #156 Add test proving `max_load()` invariants [Merged]

> Username: cmazakas  
> Created at: 2022-10-31 21:18:57 UTC  
> Updated at: 2022-11-01 16:05:18 UTC  
> Merged at: 2022-11-01 16:05:18 UTC  
> Closed at: 2022-11-01 16:05:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/156  

Adds a simple unit test to prove that insertions up to the `max_load` do not cause a rehash and that any subsequent insertions do cause the rehash and the max_load to correspondingly grow.

---
