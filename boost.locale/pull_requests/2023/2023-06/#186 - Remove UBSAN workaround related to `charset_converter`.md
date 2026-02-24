# #186 Remove UBSAN workaround related to `charset_converter` [Merged]

> Username: Flamefire  
> Created at: 2023-06-23 16:30:38 UTC  
> Updated at: 2023-06-25 16:23:03 UTC  
> Merged at: 2023-06-24 08:24:45 UTC  
> Closed at: 2023-06-24 08:24:45 UTC  
> Url: https://github.com/boostorg/locale/pull/186  

To avoid the UBSAN failure it is enough to mark the class as VISIBLE similar to e.g. exception types.  
This allows to remove the workaround with custom deleter and UBSAN-supression macros which makes the code more readable

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-23 21:28:52 UTC  
> Url: https://github.com/boostorg/locale/pull/186#issuecomment-1605000526  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#186](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (23e7967) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/d50fc5941bc12567eae0282e82e3f2af1cdcf5a5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d50fc59) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/186/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #186      +/-   ##  
===========================================  
- Coverage    92.79%   92.79%   -0.01%       
===========================================  
  Files          112      112                
  Lines         9897     9891       -6       
===========================================  
- Hits          9184     9178       -6       
  Misses         713      713                
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/codepage.cpp](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb2RlcGFnZS5jcHA=) | `97.53% <ø> (ø)` | |  
| [test/test\_encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VuY29kaW5nLmNwcA==) | `97.49% <ø> (ø)` | |  
| [include/boost/locale/detail/encoding.hpp](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGV0YWlsL2VuY29kaW5nLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/encoding.hpp](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/186/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d50fc59...23e7967](https://app.codecov.io/gh/boostorg/locale/pull/186?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
