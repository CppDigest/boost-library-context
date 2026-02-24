# #194 Remove extraneous semicolons [Merged]

> Username: cmazakas  
> Created at: 2023-06-21 14:49:56 UTC  
> Updated at: 2023-06-21 22:35:08 UTC  
> Merged at: 2023-06-21 22:35:05 UTC  
> Closed at: 2023-06-21 22:35:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/194  

This should fix https://github.com/boostorg/unordered/issues/190  
  
Note: I didn't add `-Wextra-semi -Wextra-semi-stmt` to the testing Jamfile because Boost.Move has a single extraneous `;` as well and because we use `-Werror`, this breaks _our_ build until Move is fixed.  
  
Because we're going to drop Move as a dependency in the next release, we can add `-Wextra-semi -Wextra-semi-stmt` to the test Jamfile then.  
  
This PR was accomplished by manually hand-editing Boost.Move and then running the test suite locally with:  
```  
<toolset>clang-14:<cxxflags>"-Wunused-template -Wextra-semi -Wextra-semi-stmt"  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-06-21 15:08:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/194#issuecomment-1601019179  

Do please submit a PR against Boost.Move with the necessary changes.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-06-21 20:45:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/194#issuecomment-1601648661  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#194](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (dce3860) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/7d39b68e466dfb178b86fd5018b1c4bed986cf70?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7d39b68) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/194/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #194   +/-   ##  
========================================  
  Coverage    97.84%   97.84%             
========================================  
  Files          122      122             
  Lines        18583    18583             
========================================  
  Hits         18182    18182             
  Misses         401      401             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/concurrent\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X21hcC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `99.82% <ø> (ø)` | |  
| [test/helpers/invariants.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9oZWxwZXJzL2ludmFyaWFudHMuaHBw) | `74.54% <ø> (ø)` | |  
| [test/unordered/compile\_tests.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvY29tcGlsZV90ZXN0cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/unordered/detail/implementation.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.35% <100.00%> (ø)` | |  
| [test/exception/insert\_exception\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leGNlcHRpb24vaW5zZXJ0X2V4Y2VwdGlvbl90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/objects/exception.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL2V4Y2VwdGlvbi5ocHA=) | `97.00% <100.00%> (ø)` | |  
| [test/unordered/reserve\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcmVzZXJ2ZV90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/unordered/transparent\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvdHJhbnNwYXJlbnRfdGVzdHMuY3Bw) | `92.65% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7d39b68...dce3860](https://app.codecov.io/gh/boostorg/unordered/pull/194?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
