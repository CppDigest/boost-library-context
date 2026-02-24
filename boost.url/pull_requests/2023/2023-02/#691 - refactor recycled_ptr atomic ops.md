# #691 refactor recycled_ptr atomic ops [Merged]

> Username: alandefreitas  
> Created at: 2023-02-22 02:45:27 UTC  
> Updated at: 2024-02-21 00:56:18 UTC  
> Merged at: 2023-02-22 14:43:22 UTC  
> Closed at: 2023-02-22 14:43:22 UTC  
> Url: https://github.com/boostorg/url/pull/691  



---

## Comment 1

> Username: alandefreitas  
> Created_at: 2023-02-22 02:46:04 UTC  
> Url: https://github.com/boostorg/url/pull/691#issuecomment-1439361788  

This?  
  
@pdimov

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-02-22 02:59:39 UTC  
> Url: https://github.com/boostorg/url/pull/691#issuecomment-1439370959  

GCOVR code coverage report [https://691.url.prtest.cppalliance.org/gcovr/index.html](https://691.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://691.url.prtest.cppalliance.org/genhtml/index.html](https://691.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://691.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://691.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-02-22 03:03:26 UTC  
> Updated_at: 2023-02-22 03:06:24 UTC  
> Url: https://github.com/boostorg/url/pull/691#issuecomment-1439373196  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/691?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#691](https://codecov.io/gh/boostorg/url/pull/691?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9169c28) into [develop](https://codecov.io/gh/boostorg/url/commit/1ff5ea715487ab0fc2e47f305a90c93e655bdab1?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1ff5ea7) will **increase** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/691/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/691?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #691      +/-   ##  
===========================================  
+ Coverage    97.12%   97.13%   +0.01%       
===========================================  
  Files          152      152                
  Lines         8445     8445                
===========================================  
+ Hits          8202     8203       +1       
+ Misses         243      242       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/691?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/grammar/detail/impl/recycled.ipp](https://codecov.io/gh/boostorg/url/pull/691?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZXRhaWwvaW1wbC9yZWN5Y2xlZC5pcHA=) | `100.00% <100.00%> (+4.34%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/691?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/691?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1ff5ea7...9169c28](https://codecov.io/gh/boostorg/url/pull/691?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-02-22 03:24:14 UTC  
> Url: https://github.com/boostorg/url/pull/691#issuecomment-1439385600  

Yes.

---
