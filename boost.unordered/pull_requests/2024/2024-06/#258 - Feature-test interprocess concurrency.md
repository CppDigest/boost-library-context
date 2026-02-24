# #258 Feature/test interprocess concurrency [Merged]

> Username: joaquintides  
> Created at: 2024-06-21 15:27:40 UTC  
> Updated at: 2024-06-22 07:13:34 UTC  
> Merged at: 2024-06-22 07:13:30 UTC  
> Closed at: 2024-06-22 07:13:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/258  

Verifies that concurrent containers work when accessed through shared memory by different processes, including if stats are enabled (which case didn't work before).  
  
Addresses #256.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-21 21:46:56 UTC  
> Updated_at: 2024-06-22 02:14:51 UTC  
> Url: https://github.com/boostorg/unordered/pull/258#issuecomment-2183505703  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/258?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.06%. Comparing base [(`a166a56`)](https://app.codecov.io/gh/boostorg/unordered/commit/a166a56401be9aa1edee89912374dee47ced7c68?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1a8c685`)](https://app.codecov.io/gh/boostorg/unordered/commit/1a8c6853c6ddbaeb428c4924fa57f64a3f3a587a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/258/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/258?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #258   +/-   ##  
========================================  
  Coverage    98.06%   98.06%             
========================================  
  Files          151      152    +1       
  Lines        20509    20557   +48       
========================================  
+ Hits         20112    20160   +48       
  Misses         397      397             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/258?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fcore.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `99.54% <ø> (ø)` | |  
| [...de/boost/unordered/detail/foa/cumulative\_stats.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fcumulative_stats.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jdW11bGF0aXZlX3N0YXRzLmhwcA==) | `95.55% <ø> (ø)` | |  
| [test/cfoa/interprocess\_concurrency\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/258?src=pr&el=tree&filepath=test%2Fcfoa%2Finterprocess_concurrency_tests.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2ludGVycHJvY2Vzc19jb25jdXJyZW5jeV90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/258?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/258?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a166a56...1a8c685](https://app.codecov.io/gh/boostorg/unordered/pull/258?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cmazakas  
> Created_at: 2024-06-21 22:35:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/258#issuecomment-2183546844  

Hmm, this PR makes me wonder if we can dramatically simplify the shared memory names in the mmap_tests.  
  
Looking at the previous test compared to this one, the old seems unideal in comparison.

---

## Comment 3

> Username: joaquintides  
> Created_at: 2024-06-22 07:13:19 UTC  
> Url: https://github.com/boostorg/unordered/pull/258#issuecomment-2183888390  

> Hmm, this PR makes me wonder if we can dramatically simplify the shared memory names in the mmap_tests.  
  
Yes, the difference is that the old code prepends a fixed string to the randomly generated UUID. I think this is superfluous as `boost::uuids::random_generator()()` provides the necessary diversity.

---
