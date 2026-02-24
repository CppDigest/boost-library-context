# #106 - Tests do not pass with current master branch in C++03 and C++14 [Open]

> Username: glenfe  
> Created at: 2020-11-29 14:55:04 UTC  
> Updated at: 2021-03-26 17:42:08 UTC  
> Url: https://github.com/boostorg/ublas/issues/106  

The current master branch is what is being released in Boost 1.75 but these failures were also present in the Boost 1.74 release it seems.  
  
C++20 was also failing but it passes now that  I cherry-picked the allocator related fixes from develop to master. C++11 and C++17 pass.
