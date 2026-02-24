# #188 Micro-optimize PFR fields detection [Merged]

> Username: apolukhin  
> Created at: 2024-10-13 14:07:12 UTC  
> Updated at: 2024-10-15 06:39:29 UTC  
> Merged at: 2024-10-15 06:39:26 UTC  
> Closed at: 2024-10-15 06:39:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/188  

* Start upper bound fields search from `4` fields, to avoid slow startup on typical workloads  
* Inline the `fields_count_binary_search_unbounded` function to reduce template instantiations depth by 1  
* Renamed `min` to `min_of_size_t` to avoid weired syntax  
* Applied idea of better error reporting from #120  
* Do not start fields count computation if one of the static asserts failed. That speedups error reporting in edge cases  
* Use `std::*_t` versions of traits as they are faster in some implementations  
* Rewrite binary search to simplify it and to avoid degradation to linear search on types that have constructor from variadic pack  
* Remove default template parameters to simplify code  
  
As a result, the whole test suite now runs 10%-25% faster on MSVC, ~20% faster on Clang, and 7%-20% faster on GCC.

---

## Comment 1

> Username: coveralls  
> Created_at: 2024-10-13 14:18:28 UTC  
> Updated_at: 2024-10-15 05:27:37 UTC  
> Url: https://github.com/boostorg/pfr/pull/188#issuecomment-2408998102  

## Pull Request Test Coverage Report for [Build 11339766261](https://coveralls.io/builds/70333951)  
  
  
### Details  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/70333951/badge)](https://coveralls.io/builds/70333951) |  
| :-- | --: |  
| Change from base [Build 11253776824](https://coveralls.io/builds/70245713): |  0.0% |  
| Covered Lines: | 407 |  
| Relevant Lines: | 407 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---
