# #42 Made some subfilters' lookup branchless [Merged]

> Username: joaquintides  
> Created at: 2025-09-13 11:01:09 UTC  
> Updated at: 2025-09-13 16:44:36 UTC  
> Merged at: 2025-09-13 16:44:34 UTC  
> Closed at: 2025-09-13 16:44:34 UTC  
> Url: https://github.com/boostorg/bloom/pull/42  

Made lookup implementation branchless for `block`, `fast_multiblock32` and `fast_multiblock64`, which results in some performance gains, particularly for mixed successful/unsuccessful queries.  
  
**Benchmark results**  
  
https://github.com/boostorg/boost_bloom_benchmarks/tree/branchful-vs-branchless-comparison  
  
In addition to branchful/branchless, we have also tested with prefetch omission, but results there are inconclusive.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-09-13 11:10:47 UTC  
> Url: https://github.com/boostorg/bloom/pull/42#issuecomment-3288107543  

An automated preview of the documentation is available at [https://42.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://42.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---
