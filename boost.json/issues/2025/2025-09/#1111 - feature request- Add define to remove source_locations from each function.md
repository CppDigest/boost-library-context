# #1111 - feature request: Add define to remove source_locations from each function [Open]

> Username: kelbon  
> Created at: 2025-09-11 19:03:39 UTC  
> Updated at: 2025-09-15 17:31:43 UTC  
> Url: https://github.com/boostorg/json/issues/1111  

I dont see much reasons to have source location on each function as default arguments. Json is a hot path of many apps, performance is critical here, why there are source location on each .at without possibility to remove it?  
I already have stacktrace from exception, i dont need source location

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-09-11 19:44:03 UTC  
> Url: https://github.com/boostorg/json/issues/1111#issuecomment-3282401391  

Interesting. I now wonder what is the performance overhead of the source locations...

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-09-15 12:09:46 UTC  
> Url: https://github.com/boostorg/json/issues/1111#issuecomment-3291846198  

The original PR doesn't show any performance degradation in automatic benchamarking: https://github.com/boostorg/json/pull/835. Admittedly, those benchmarks don't really use throwing functions. I tried locally the benchmarks that do use those. The results aren't that significant either. So, as Vinnie said, measurements that show that such an option would result in improved performance are welcome.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2025-09-15 17:31:43 UTC  
> Url: https://github.com/boostorg/json/issues/1111#issuecomment-3293221385  

Absent measurements it would be cool to see the generated object code. We could feature that in the Boost Diagnostics Suite literature, to show how it doesn't add measurable overhead (assuming that is the case)
