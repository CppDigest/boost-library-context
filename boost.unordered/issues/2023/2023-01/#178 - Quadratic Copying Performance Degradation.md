# #178 - Quadratic Copying Performance Degradation [Open]

> Username: cmazakas  
> Created at: 2023-01-04 18:16:51 UTC  
> Updated at: 2023-01-04 18:16:51 UTC  
> Url: https://github.com/boostorg/unordered/issues/178  

Inserting in "hash order" can dramatically degrade the performance of insertion. This can be easily triggered by:  
  
```cpp  
unordered_flat_map<int, int> map1(...);  
unordered_flat_map<int, int> map2;  
for (auto const& kv: map1) { map2.insert(kv); }  
```  
  
For users, this can be circumvented easily by either using the copy constructor or by calling `reserve()` prior to insertion.
