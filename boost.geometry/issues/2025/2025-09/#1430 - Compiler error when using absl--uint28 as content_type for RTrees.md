# #1430 - Compiler error when using absl::uint28 as content_type for RTrees [Closed]

> Username: totakura  
> Created at: 2025-09-15 10:09:32 UTC  
> Updated at: 2025-10-09 17:57:28 UTC  
> Closed at: 2025-10-09 17:57:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/1430  

Compile fails with the error:  
```  
...  
../boost/geometry/index/detail/rtree/rstar/choose_next_node.hpp:135:33: error: conversion from 'type' (aka 'double') to 'uint128' is ambiguous  
  135 |         if ( min_content_diff < -std::numeric_limits<double>::epsilon() || std::numeric_limits<double>::epsilon() < min_content_diff )  
      |                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
...  
```
