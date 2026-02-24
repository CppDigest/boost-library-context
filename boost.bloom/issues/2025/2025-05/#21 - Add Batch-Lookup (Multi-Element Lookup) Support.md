# #21 - [peer-review] Add Batch-Lookup (Multi-Element Lookup) Support [Closed]

> Username: Becheler  
> Created at: 2025-05-31 18:31:10 UTC  
> Updated at: 2025-06-24 13:27:44 UTC  
> Closed at: 2025-06-24 13:27:44 UTC  
> Url: https://github.com/boostorg/bloom/issues/21  

## Description  
  
Дмитрий Архипов (May 21):  
> “After thinking about it for several days I could only conceive of one possible extra operation: looking for several items at once may potentially be faster than doing it in sequence.”  
  
---  
  
## Requested Outcome  
  
- [ ] Investigate a batch-lookup API that processes multiple elements in a single pass, eg:  
    - `filter.batch_may_contain(container_of_keys)`  
    - `filter.may_contain(key1, key2, key3)`  
- [ ] Benchmark performance against individual lookups to verify any efficiency gains.  
- [ ] Document the new API and provide usage examples if implemented.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 08:13:24 UTC  
> Updated at: 2025-06-13 08:30:21 UTC  
> Url: https://github.com/boostorg/bloom/issues/21#issuecomment-2969488168  

[Added bulk operations to roadmap](https://github.com/joaquintides/bloom/commit/585bb5fbf087d488886fc20c48e3dc6792d6f8f7)

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-24 13:27:44 UTC  
> Url: https://github.com/boostorg/bloom/issues/21#issuecomment-3000495021  

I will close this issue now that the bulk operations have been added to the roadmap :)
