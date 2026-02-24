# #217 Feature/bulk visit [Merged]

> Username: joaquintides  
> Created at: 2023-10-09 09:36:23 UTC  
> Updated at: 2023-10-11 10:50:32 UTC  
> Merged at: 2023-10-11 10:50:28 UTC  
> Closed at: 2023-10-11 10:50:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/217  

Some points for discussion:  
* I opted for a very generic `visit(first, last, f)` API where `std::distance(first, last)` is not known at compile time: benchmarks indicate there's no significant performance penalty vs. a fixed-size API.  
* I used plain `"visit"` rather than say `"bulk_visit"` for symmetry with other iterator operations like `insert(first, last)`.  
* The iterators passed have to be forward (at least). The way I've implemented it, we check the iterator category in C++11-C++17 and the concept `std::forward_iterator` in C++20 and later: these two checks are not exactly equivalent.  
* If  `Hash`, `Pred`  are not transparent, `*first` must dereference _exactly_ to a (const) (reference to) `key_type`, instead of allowing for implicit conversions to `key_type`.  This is done out of conservatism cause `*first` can be called twice or more times for each key, and the conversion may be expensive.  
* I'm not providing a ready-made buffering utility such as devised [here](https://github.com/boostorg/boost_unordered_benchmarks/blob/c3f2ad693b09406566c955243949703562788322/parallel_load.cpp#L153-L182): I think this is easy enough for the user to implement by themselves that we don't need to bother.  
* `bulk_visit_size` is a constant member value of the container rather than a global constant, even though it's the same everywhere (16). I feel this allows for more flexibility looking forward, but the ergonomics are a little worse for users implementing their buffering utilities.  
* Benchmarks look very good, except: in GCC 12 x86, including `gtl::parallel_flat_hash_map` in the benchmarks makes the performance of `boost::concurrent_flat_map` in bulk mode [degrade](https://github.com/boostorg/boost_unordered_benchmarks/commit/6c160eb6d2193c6aa3352130fe25c216413b42e7) (!!). This does not make sense at all, but such is life. If you have a clue please let me know.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-10-09 09:47:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/217#issuecomment-1752685041  

An automated preview of the documentation is available at [https://217.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://217.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-10-09 11:55:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/217#issuecomment-1752869215  

It might be useful for user to know whether `unordered_x<...>::bulk_visit_size` depends on the template parameters of the container, but I'm not quite sure how to express that.

---

## Comment 3

> Username: joaquintides  
> Created_at: 2023-10-09 15:32:28 UTC  
> Updated_at: 2023-10-09 15:32:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/217#issuecomment-1753234073  

Well, the fact that `bulk_visit_size` is a nested value of `concurrent_x<...>` implies that it depends on `...`.  If we want to communicate that this is not the case, we just define it as a lobal constant ourside `concurrent_x`.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-10-09 15:34:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/217#issuecomment-1753237989  

Does it have to depend on them, though? Or on all of them? How would a different hash function possibly affect it?

---

## Comment 5

> Username: joaquintides  
> Created_at: 2023-10-09 15:49:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/217#issuecomment-1753259756  

Well, if we were to fine tune `bulk_visit_size`, it'd depend on `Hash` (the faster `Hash`, the larger `bulk_visit_size` ought to be) and on the CPU architecture (memory fetching time/instruction throughput). No other dependencies, really. The hot code is this:  
  
https://github.com/boostorg/unordered/pull/217/files#diff-a299ea8b15c2c2c34afbb92c11f11dc383bb2764a341c3e145090b6df0170720R1207-R1221  
  
Maybe all these considerations are moot, anyway, as we can always increase `bulk_visit_size` wholesale is there's a need: there should be no practical downside to having this constant larger than the minimum required to achieve no waiting cycles --more data stored on the stack, that's all.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2023-10-10 00:23:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/217#issuecomment-1754108903  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#217](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5afa714) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/44b669a71c9565a592187c31b392d59dc393fe9d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (44b669a) will **decrease** coverage by `0.03%`.  
> Report is 1 commits behind head on develop.  
> The diff coverage is `98.41%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/217/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #217      +/-   ##  
===========================================  
- Coverage    98.12%   98.10%   -0.03%       
===========================================  
  Files          142      143       +1       
  Lines        19502    20099     +597       
===========================================  
+ Hits         19137    19718     +581       
- Misses         365      381      +16       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/concurrent\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X21hcC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/concurrent\_flat\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X3NldC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.68% <100.00%> (+0.02%)` | :arrow_up: |  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `99.84% <ø> (-0.16%)` | :arrow_down: |  
| [test/cfoa/visit\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL3Zpc2l0X3Rlc3RzLmNwcA==) | `99.19% <96.92%> (-0.35%)` | :arrow_down: |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/217/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [44b669a...5afa714](https://app.codecov.io/gh/boostorg/unordered/pull/217?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
