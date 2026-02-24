# #12 Guard assertion about moved-from state of unordered_map [Closed]

> Username: BillyONeal  
> Created at: 2019-02-05 20:48:00 UTC  
> Updated at: 2019-03-07 00:09:11 UTC  
> Closed at: 2019-02-06 11:25:30 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/12  

See discussion in https://github.com/boostorg/poly_collection/issues/11 -- basically, this test wants the moved-from state of an unordered_map to be empty, but there is no such requirement in the standard, and a natural implementation of unordered_map for non-POCMA doesn't empty the assigned-from container.

---

## Comment 1

> Username: joaquintides  
> Created_at: 2019-02-06 11:25:30 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/12#issuecomment-460989820  

Superseded by d13e8ba7ba4e67a29eb102cb4c700776db86d4fc.

---
