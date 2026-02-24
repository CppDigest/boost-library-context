# #387 Improve axis regular precision [Open]

> Username: ryanelandt  
> Created at: 2023-06-15 23:41:28 UTC  
> Updated at: 2025-08-16 04:43:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/387  

This PR refactors regular axis to address the issues described in #336 by incorporating some of the ideas from #386. This PR adds a unit that verifies exact indexing for various cases.  
  
The output of `./benchmark_axis_index` before the refactor:    
```  
Benchmark                         Time             CPU   Iterations  
-------------------------------------------------------------------  
regular<uniform>               1.31 ns         1.31 ns    538240574  
regular<normal>                2.00 ns         2.00 ns    351784388  
circular<uniform>              1.62 ns         1.62 ns    432490637  
circular<normal>               1.62 ns         1.62 ns    424151491  
```  
  
The output of `./benchmark_axis_index` after the refactor:  
```  
Benchmark                         Time             CPU   Iterations  
-------------------------------------------------------------------  
regular<uniform>               1.13 ns         1.13 ns    578725526  
regular<normal>                1.71 ns         1.71 ns    399524265  
circular<uniform>              1.51 ns         1.51 ns    471027721  
circular<normal>               1.49 ns         1.49 ns    459937000  
```

---

## Comment 1

> Username: ryanelandt  
> Created_at: 2023-06-20 16:38:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/387#issuecomment-1599140177  

This PR does not change the public API, but it does change private class members that are not user accessible. Modifying private class members has implications for serialization. Is this type of PR problematic in regards to Boost API stability?

---

## Comment 2

> Username: emmenlau  
> Created_at: 2025-01-28 15:24:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/387#issuecomment-2619317796  

This looks actually quite promising, and performance is better than with our (naiive) solution. Could this be considered for integration?

---

## Comment 3

> Username: henryiii  
> Created_at: 2025-08-16 04:43:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/387#issuecomment-3193382887  

By the way, I think this would help https://github.com/scikit-hep/hist/issues/455.

---
