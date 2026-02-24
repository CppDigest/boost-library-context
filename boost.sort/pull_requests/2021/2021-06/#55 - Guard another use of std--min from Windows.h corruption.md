# #55 Guard another use of std::min from Windows.h corruption [Merged]

> Username: joemmett  
> Created at: 2021-06-16 15:21:14 UTC  
> Updated at: 2021-06-20 01:55:36 UTC  
> Merged at: 2021-06-20 01:55:36 UTC  
> Closed at: 2021-06-20 01:55:36 UTC  
> Url: https://github.com/boostorg/sort/pull/55  

The include order in test_block_indirect_sort will include Windows.h before sort.hpp on Windows, letting Windows.h corrupt unparenthesized uses of std::min.

---
