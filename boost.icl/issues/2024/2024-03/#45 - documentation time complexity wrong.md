# #45 - documentation time complexity wrong [Open]

> Username: jhcarl0814  
> Created at: 2024-03-28 02:37:48 UTC  
> Updated at: 2024-03-28 02:37:48 UTC  
> Url: https://github.com/boostorg/icl/issues/45  

- Complexity - 1.84.0 (https://www.boost.org/doc/libs/1_84_0/libs/icl/doc/html/boost_icl/implementation/complexity.html )  
In "Table 1.15. Time Complexity of Addition", `interval set/separate interval set/split interval set/interval map/split interval map += interval_sets/interval_maps` 's time complexity is `O(m log(n+m))`, but this time complexity should be at least `interval set/separate interval set/split interval set/interval map/split interval map += T::segment_type` 's time complexity (`O(n)`). Example: the `interval_sets/interval_maps` contains only one big interval (whose type is `T::segment_type`), and this interval makes `interval set/separate interval set/split interval set/interval map/split interval map += T::segment_type` 's time complexity `O(n)`.  
  
- Addition - 1.84.0 (https://www.boost.org/doc/libs/1_84_0/libs/icl/doc/html/boost_icl/function_reference/addition.html )  
In "Table 1.23. Time Complexity for inplace Addition on interval containers", `interval_set/separate_interval_set += interval_sets`, `split_interval_set += interval_sets` and `interval_maps += interval_maps` have wrong time complexity.  
  
- Subtraction - 1.84.0 (https://www.boost.org/doc/libs/1_84_0/libs/icl/doc/html/boost_icl/function_reference/subtraction.html )  
In "Table 1.26. Time Complexity for inplace Subtraction on interval containers", `interval_sets -= interval sets` and `interval_maps -= interval sets/interval maps` have wrong time complexity.  
  
- Insertion - 1.84.0 (https://www.boost.org/doc/libs/1_84_0/libs/icl/doc/html/boost_icl/function_reference/insertion.html )  
In "Table 1.29. Time Complexity for inplace insertion on interval containers", `insert(interval_set/separate_interval_set, interval sets)`, `insert(split_interval_set, interval sets)` and `insert(interval_maps, interval maps)` have wrong time complexity..  
  
- Intersection - 1.84.0 (https://www.boost.org/doc/libs/1_84_0/libs/icl/doc/html/boost_icl/function_reference/intersection.html )  
In "Table 1.36. Time Complexity for inplace intersection on interval containers", `interval_sets &= interval sets` and `interval_maps &= interval sets/interval maps` have wrong time complexity.  
  
- Symmetric Difference - 1.84.0 (https://www.boost.org/doc/libs/1_84_0/libs/icl/doc/html/boost_icl/function_reference/symmetric_difference.html )  
In "Table 1.39. Time Complexity for inplace symmetric difference on interval containers", `interval_sets ^= interval sets` and `interval_maps ^= interval sets/interval maps` have wrong time complexity.
