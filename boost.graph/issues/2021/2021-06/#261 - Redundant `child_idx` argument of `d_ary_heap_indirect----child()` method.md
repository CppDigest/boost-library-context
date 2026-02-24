# #261 - Redundant `child_idx` argument of `d_ary_heap_indirect<>::child()` method [Closed]

> Username: qbit86  
> Created at: 2021-06-01 22:13:11 UTC  
> Updated at: 2022-03-04 02:10:19 UTC  
> Closed at: 2022-03-04 02:10:19 UTC  
> Url: https://github.com/boostorg/graph/issues/261  

[child](https://github.com/boostorg/graph/blob/d865589d62afba7ca590bfcaf76f0723f8494dd4/include/boost/graph/detail/d_ary_heap.hpp#L208..L211) method takes `child_idx` as an argument.  
```cpp  
static size_type child(size_type index, std::size_t child_idx)  
{  
    return index * Arity + child_idx + 1;  
}  
```  
But is never called with indices other than [0](https://github.com/boostorg/graph/blob/d865589d62afba7ca590bfcaf76f0723f8494dd4/include/boost/graph/detail/d_ary_heap.hpp#L307).  
```cpp  
size_type first_child_index = child(index, 0);  
```  
So why not get rid of it altogether, as in [other implementations](https://github.com/dotnet/runtime/blob/d3e35e7fd3dad543c275d1f1e10fc0ba91d70a15/src/libraries/System.Collections/src/System/Collections/Generic/PriorityQueue.cs#L586)?  
```cs  
private int GetFirstChildIndex(int index) => (index << Log2Arity) + 1;  
```
