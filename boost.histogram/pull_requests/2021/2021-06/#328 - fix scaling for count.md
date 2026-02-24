# #328 fix scaling for count [Merged]

> Username: HDembinski  
> Created at: 2021-06-26 11:59:08 UTC  
> Updated at: 2021-06-27 14:58:15 UTC  
> Merged at: 2021-06-27 14:58:14 UTC  
> Closed at: 2021-06-27 14:58:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/328  

accumulators::count was missing a test for scaling and did not work when the second parameter was true. Proper scaling is now implemented (thanks to @henryiii for reporting this).  
  
The rest of this patch repairs the CI system.

---
