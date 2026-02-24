# #172 feature/foa fast copy [Merged]

> Username: joaquintides  
> Created at: 2022-12-01 17:51:53 UTC  
> Updated at: 2024-08-17 16:49:04 UTC  
> Merged at: 2022-12-09 18:05:54 UTC  
> Closed at: 2022-12-09 18:05:54 UTC  
> Url: https://github.com/boostorg/unordered/pull/172  

When copy constructing or copy assigning an unordered flat container, if the source and target bucket arrays have the same size, then:  
* The metadata array is copied via `memcpy`.  
* If `value_type` is trivially copy assignable, a single `memcpy` call is used to copy all the elements;  
* else, elements are copied one by one but directly into place, i.e. bypassing the standard insertion algorithm.  
  
This should speed up copy/copy assignment by an order of magnitude under the right conditions (haven't measured), and will make us look good in the related test on Martin's benchmarks.

---

## Review 1 [Approved]

> Username: pdimov  
> Created_at: 2022-12-01 18:06:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/unordered/pull/172#pullrequestreview-1201457038  

Looks good to me at a quick glance, but I haven't checked whether our copy tests are extensive enough to exercise all these cases.

---

## Comment 2

> Username: joaquintides  
> Created_at: 2022-12-01 18:26:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/172#issuecomment-1334176552  

Codecov says all the new code is exercised:  
https://app.codecov.io/gh/boostorg/unordered/blob/feature%2Ffoa_fast_copy/include/boost/unordered/detail/foa.hpp

---

## Review 3 [Approved]

> Username: cmazakas  
> Created_at: 2022-12-01 18:54:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/unordered/pull/172#pullrequestreview-1201516805  

I'm not sure how else to ensure a failing test case aside from adding a static assertion somewhere in the copy tests that we're using a trivially copyable type but this otherwise seems great to me.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2022-12-01 18:56:38 UTC  
> Updated_at: 2022-12-01 19:01:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/172#issuecomment-1334208135  

Shall we merge now or do we keep develop untouched till Boost 1.81 just in case some last-minute fix is needed?

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-12-01 19:00:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/172#issuecomment-1334213382  

Maybe just wait, it's two weeks.

---

## Comment 6

> Username: pavel-odintsov  
> Created_at: 2022-12-01 19:07:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/172#issuecomment-1334223042  

That's impressive stuff! I think you remember my weird questions about ways to copy structure as fast as possible :)

---

## Comment 7

> Username: joaquintides  
> Created_at: 2022-12-02 07:57:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/172#issuecomment-1334876099  

Hi Pavel, thank you! Are you going to give `boost::unordered_flat_map` a try with FastNetMon?

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2022-12-02 10:06:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/172#issuecomment-1335019723  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/172?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#172](https://codecov.io/gh/boostorg/unordered/pull/172?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1d99854) into [develop](https://codecov.io/gh/boostorg/unordered/commit/99bd9d9d31eec049b6c879e47f3f4ef33c265e18?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (99bd9d9) will **increase** coverage by `0.34%`.  
> The diff coverage is `97.43%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/172/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #172      +/-   ##  
===========================================  
+ Coverage    97.65%   98.00%   +0.34%       
===========================================  
  Files           83       83                
  Lines        12034    14045    +2011       
===========================================  
+ Hits         11752    13765    +2013       
+ Misses         282      280       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/172/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `99.23% <97.43%> (+1.30%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/172/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX21hcC5ocHA=) | `99.82% <0.00%> (-0.03%)` | :arrow_down: |  
| [test/unordered/deduction\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/172/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZGVkdWN0aW9uX3Rlc3RzLmNwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/unordered/detail/prime\_fmod.hpp](https://codecov.io/gh/boostorg/unordered/pull/172/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3ByaW1lX2Ztb2QuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/unordered/detail/implementation.hpp](https://codecov.io/gh/boostorg/unordered/pull/172/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.36% <0.00%> (+0.19%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/172/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX3NldC5ocHA=) | `99.89% <0.00%> (+0.25%)` | :arrow_up: |  
| [test/unordered/prime\_fmod\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/172/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcHJpbWVfZm1vZF90ZXN0cy5jcHA=) | `96.12% <0.00%> (+2.07%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/172?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/172?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [99bd9d9...1d99854](https://codecov.io/gh/boostorg/unordered/pull/172?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 9

> Username: pavel-odintsov  
> Created_at: 2023-03-21 11:03:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/172#issuecomment-1477639437  

According to Joaquín M López Muñoz this thing will be part of Boost 1.82.

---
