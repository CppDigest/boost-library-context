# #163 - Implement group14 for foa containers [Closed]

> Username: cmazakas  
> Created at: 2022-11-14 18:42:41 UTC  
> Updated at: 2023-03-20 15:47:08 UTC  
> Closed at: 2023-03-20 15:47:07 UTC  
> Url: https://github.com/boostorg/unordered/issues/163  

Consider a new `group14` structure with uses 14 bytes for reduced hashes and 2 bytes for marking overflow.  
  
Compare performance against current `group15` implementation.

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-03-20 15:47:07 UTC  
> Url: https://github.com/boostorg/unordered/issues/163#issuecomment-1476482827  

This was implemented and experimented with here:  
https://github.com/boostorg/boost_unordered_benchmarks/tree/boost_unordered_flat_map_group14  
  
Because of the performance trade-offs, this implementation is not going to be followed through with.
