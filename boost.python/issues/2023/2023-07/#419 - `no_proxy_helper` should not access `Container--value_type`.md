# #419 - `no_proxy_helper` should not access `Container::value_type` [Open]

> Username: MichaelSuen-thePointer  
> Created at: 2023-07-28 08:37:58 UTC  
> Updated at: 2023-07-28 08:38:45 UTC  
> Url: https://github.com/boostorg/python/issues/419  

[see code here](https://github.com/boostorg/python/blob/master/include/boost/python/suite/indexing/detail/indexing_suite_detail.hpp#L487)  
  
Some containers may not provide proper `value_type` member, and the user can still manually passing its element type as `Key` or `Data` template parameter to `indexing_suite`. So `indexing_suite` already know the container's value type, and does not need containers to have `::value_type`. Its detailed implementation should then directly reading it from `indexing_suite`, not from `Container::value_type` again.
