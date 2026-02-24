# #145 Fix move assign noexcept violation/perf regression [Merged]

> Username: cmazakas  
> Created at: 2022-09-12 20:19:38 UTC  
> Updated at: 2022-09-13 19:32:34 UTC  
> Merged at: 2022-09-13 18:46:38 UTC  
> Closed at: 2022-09-13 18:46:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/145  

An unconditional `reserve()` call was added to move assignment breaking noexcept invariants and pessimisizing performance when POCMA is false but the allocators still compare equal.  
  
This fixes a gcc-4.7 CI failure in #144 and should be merged first.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-12 22:06:30 UTC  
> Updated_at: 2022-09-12 22:51:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/145#issuecomment-1244574164  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/145?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#145](https://codecov.io/gh/boostorg/unordered/pull/145?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (dea6ce1) into [develop](https://codecov.io/gh/boostorg/unordered/commit/010dfa52e35c6048b3d2c2bf95f5df1f5976757b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (010dfa5) will **increase** coverage by `0.03%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/145/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/145?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #145      +/-   ##  
===========================================  
+ Coverage    97.41%   97.45%   +0.03%       
===========================================  
  Files           75       75                
  Lines        10331    10369      +38       
===========================================  
+ Hits         10064    10105      +41       
+ Misses         267      264       -3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/145?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/implementation.hpp](https://codecov.io/gh/boostorg/unordered/pull/145/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.16% <ø> (+0.24%)` | :arrow_up: |  
| [test/unordered/noexcept\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/145/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbm9leGNlcHRfdGVzdHMuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/145?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/145?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [010dfa5...dea6ce1](https://codecov.io/gh/boostorg/unordered/pull/145?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Review 2 [Approved]

> Username: joaquintides  
> Created_at: 2022-09-13 17:36:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/unordered/pull/145#pullrequestreview-1106203039  

OK by me. Can you explain to me why this spurious `reserve` triggered a *termination* on GCC4.7 and nowhere else?

---

## Comment 3

> Username: cmazakas  
> Created_at: 2022-09-13 18:36:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/145#issuecomment-1245809978  

> OK by me. Can you explain to me why this spurious `reserve` triggered a _termination_ on GCC4.7 and nowhere else?  
  
Yup!  
  
gcc-4.7 is a fringe compiler in that it's C++11 support isn't 100%. So we were in a case where the compiler supports `noexcept` and has `std::allocator::is_always_equal` being `true_type`. This means the noexcept specification of `unordered_set::operator=(unordered&&)` can be true. However, POCMA was false.  
  
In the original noexcept_tests, we were move-assigning a small unordered_set into a bigger unordered_set so the erroneous call to reserve() had no effect as reserve() didn't used to shrink the number of buckets.  
  
The first thing I did for this PR was add a test case doing the reverse: assigning a big unordered_set into a small one to trigger a proper rehashing. Because we actually started rehashing, the Hasher was invoked which was set to throw and because noexcept was true, we got a terminate call.  
  
The implementation was internally bifurcated around POCMA. The reserve() call was erroneously placed in the POCMA => false branch and wasn't guarded by an allocator equality check, i.e. only call `reserve()` when POCMA is false and allocators are unequal.  
  
The test now checks that `a.allocate()` isn't called either.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2022-09-13 18:41:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/145#issuecomment-1245816933  

Ok, so GCC 4.7 is the *only* example where `std::allocator` has no POCMA typedef (and hence POCMA is deduced to be false) *and* `noexcept` is supported, right?  
  
Thanks for th explanation. Merging now.

---
