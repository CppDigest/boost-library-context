# #260 - Confusing call to `combine` in `astar_search_no_init_tree` [Closed]

> Username: qbit86  
> Created at: 2021-06-01 13:13:09 UTC  
> Updated at: 2022-03-03 23:55:14 UTC  
> Closed at: 2022-03-03 23:55:14 UTC  
> Url: https://github.com/boostorg/graph/issues/260  

What does this line of code mean in [astar_search.hpp](https://github.com/boostorg/graph/blob/d865589d62afba7ca590bfcaf76f0723f8494dd4/include/boost/graph/astar_search.hpp#L331)?  
```cpp  
combine(get(distance, v), e_weight);  
```  
The result of this `combine()` is not used anywhere. Actual distance update happens inside the `relax()` function.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2021-06-02 04:55:33 UTC  
> Url: https://github.com/boostorg/graph/issues/260#issuecomment-852721307  

Agreed, that call to combine has no effect. I won't claim to totally understand the tree version of A*, but it looks like that line can simply be deleted.
