# #125 Fix minmax_timer build, iteration count errors, and math errors [Merged]

> Username: jeking3  
> Created at: 2025-06-24 01:16:19 UTC  
> Updated at: 2025-06-26 03:43:14 UTC  
> Merged at: 2025-06-26 03:43:14 UTC  
> Closed at: 2025-06-26 03:43:14 UTC  
> Url: https://github.com/boostorg/algorithm/pull/125  

This example was written against an old implementation of boost::timer which has been deprecated and removed.  That interface used microseconds whereas the new one uses nanoseconds.  
  
The actual loop count never took the number of iterations into account, that was also fixed.  Note on original lines 60 and 67 that "n" was never used.  
  
The calculation of the "M items/s" was fixed:  
  
`(n*repeats) / (t.elapsed().wall / 1e9)` is the number of items per second since wall is in nanoseconds.  Divide by 1e6 to get millions of items per second.  This simplifies to 1e3 * number of passes / number of nanoseconds.

---
