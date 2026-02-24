# #164 - Implement unordered_flat_node_map POC [Closed]

> Username: cmazakas  
> Created at: 2022-11-16 16:05:02 UTC  
> Updated at: 2023-02-28 22:36:54 UTC  
> Closed at: 2023-02-28 22:36:54 UTC  
> Url: https://github.com/boostorg/unordered/issues/164  

Implement small proof-of-concept of an unordered flat map that stores elements in nodes, granting them reference stability.  
  
This would (most likely) enable a full compliant implementation of the unordered_flat_map.  
  
Attempt to wrap `unordered_flat_map<unique_ptr<K, T>>` and begin development from there.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-16 18:00:13 UTC  
> Url: https://github.com/boostorg/unordered/issues/164#issuecomment-1317432413  

`unordered_flat_set<unique_ptr<K, T>>` probably.

---

## Comment 2

> Username: joaquintides  
> Created at: 2022-12-19 18:49:00 UTC  
> Url: https://github.com/boostorg/unordered/issues/164#issuecomment-1358094025  

Done here:  
https://github.com/boostorg/boost_unordered_benchmarks/tree/boost_unordered_aggregate_poc_unordered_node_map
