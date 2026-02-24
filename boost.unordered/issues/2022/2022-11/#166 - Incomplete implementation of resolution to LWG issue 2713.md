# #166 - Incomplete implementation of resolution to LWG issue 2713 [Closed]

> Username: joaquintides  
> Created at: 2022-11-20 11:51:31 UTC  
> Updated at: 2022-11-22 18:43:41 UTC  
> Closed at: 2022-11-22 18:43:41 UTC  
> Url: https://github.com/boostorg/unordered/issues/166  

Seems like we don't have `(InputIterator,InputIterator,Allocator)` and `(std::initializer_list,Allocator)` deduction guides for set types as mandated by the resolution of [LWG2713](https://cplusplus.github.io/LWG/issue2713).

---

## Comment 1

> Username: cmazakas  
> Created at: 2022-11-21 15:51:54 UTC  
> Url: https://github.com/boostorg/unordered/issues/166#issuecomment-1322270749  

Ah, so we are missing the ones for set. This can be addressed.
