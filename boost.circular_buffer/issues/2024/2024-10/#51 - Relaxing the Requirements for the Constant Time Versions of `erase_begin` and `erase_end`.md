# #51 - Relaxing the Requirements for the Constant Time Versions of `erase_begin` and `erase_end` [Open]

> Username: ldeng-ustc  
> Created at: 2024-10-16 02:43:40 UTC  
> Updated at: 2024-10-16 02:43:40 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/51  

Currently, the constant time versions of `erase_begin` and `erase_end` are only applied when `value_type` is a scalar type. However, this requirement is overly restrictive.  
  
For example, in the case of `std::pair<int, int>`, the current `circular_buffer` requires `O(n)` time to perform `erase_end`, which is actually unnecessary.  
  
The `O(n)` version of erase seems to simply destruct elements. Therefore, would it be possible to relax the condition for the fast erase versions from `is_scalar<value_type>` to `is_trivially_destructible`, allowing types like `std::pair<int, int>` or other user-defined simple types to benefit from the faster `erase_begin` and `erase_end`?
