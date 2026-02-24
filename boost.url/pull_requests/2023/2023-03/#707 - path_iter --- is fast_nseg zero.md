# #707 path_iter "/" is fast_nseg zero [Merged]

> Username: alandefreitas  
> Created at: 2023-03-07 17:13:09 UTC  
> Updated at: 2024-02-21 00:55:11 UTC  
> Merged at: 2023-03-07 22:28:51 UTC  
> Closed at: 2023-03-07 22:28:51 UTC  
> Url: https://github.com/boostorg/url/pull/707  

This PR fixes the first issue in https://github.com/boostorg/url/issues/674  
  
`path_iter` was identifying `"/"` as having `fast_nseg == 1` while it should be `0` according to the library convention that `"/"` represents `{}`  and `"/./"` represents `{""}`.  
  
This led to counterintuitive results like:  
  
```cpp  
assert(url("/").set_path("/").buffer() == "/./");  
```  
  
A second problem with this convention is that when we normalize a path from `"/./"` to its equivalent `"/"`, the segments representation goes from `{""}` to `{}`, but there's nothing we can or should do about that.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-03-07 17:30:11 UTC  
> Url: https://github.com/boostorg/url/pull/707#issuecomment-1458560236  

GCOVR code coverage report [https://707.url.prtest.cppalliance.org/gcovr/index.html](https://707.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://707.url.prtest.cppalliance.org/genhtml/index.html](https://707.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://707.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://707.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-03-07 17:39:28 UTC  
> Updated_at: 2023-03-07 17:40:24 UTC  
> Url: https://github.com/boostorg/url/pull/707#issuecomment-1458572902  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/707?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#707](https://codecov.io/gh/boostorg/url/pull/707?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (251fa72) into [develop](https://codecov.io/gh/boostorg/url/commit/722ba1a586d25e0da313c40a465eced06ff81df6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (722ba1a) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/707/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/707?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #707   +/-   ##  
========================================  
  Coverage    97.13%   97.13%             
========================================  
  Files          155      155             
  Lines         8446     8449    +3       
========================================  
+ Hits          8204     8207    +3       
  Misses         242      242             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/707?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/url/detail/impl/any\_segments\_iter.ipp](https://codecov.io/gh/boostorg/url/pull/707?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3NlZ21lbnRzX2l0ZXIuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/707?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/707?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [722ba1a...251fa72](https://codecov.io/gh/boostorg/url/pull/707?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-03-07 17:40:49 UTC  
> Url: https://github.com/boostorg/url/pull/707#issuecomment-1458574561  

GCOVR code coverage report [https://707.url.prtest.cppalliance.org/gcovr/index.html](https://707.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://707.url.prtest.cppalliance.org/genhtml/index.html](https://707.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://707.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://707.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
