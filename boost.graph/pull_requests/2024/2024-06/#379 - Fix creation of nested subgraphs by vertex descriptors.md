# #379 Fix creation of nested subgraphs by vertex descriptors [Open]

> Username: reuther-genisys  
> Created at: 2024-06-10 13:32:28 UTC  
> Updated at: 2025-07-28 11:52:41 UTC  
> Url: https://github.com/boostorg/graph/pull/379  

When creating subgraphs on deeper levels of the subgraph tree, do not nterpret the vertex descriptors as global, but instead as vertex descriptors to the current graph.  
  
Fix to https://github.com/boostorg/graph/issues/378

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-06-18 00:05:39 UTC  
> Url: https://github.com/boostorg/graph/pull/379#issuecomment-2174657576  

Hmmm, I presume that MSVC 14.3 failed for no good reason.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2025-07-23 09:59:37 UTC  
> Url: https://github.com/boostorg/graph/pull/379#issuecomment-3106791371  

To start with, can you just merge `develop` in (or rebase)? Thanks. I thought I would be able to with one button click, but seems I can't.

---

## Comment 3

> Username: reuther-genisys  
> Created_at: 2025-07-28 11:52:41 UTC  
> Url: https://github.com/boostorg/graph/pull/379#issuecomment-3126875364  

> To start with, can you just merge `develop` in (or rebase)? Thanks. I thought I would be able to with one button click, but seems I can't.  
  
Done.

---
