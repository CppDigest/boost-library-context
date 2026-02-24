# #144 Fix order of environment variables when getting system default locale [Merged]

> Username: Flamefire  
> Created at: 2023-01-01 17:51:32 UTC  
> Updated at: 2023-01-07 17:41:54 UTC  
> Merged at: 2023-01-07 17:41:51 UTC  
> Closed at: 2023-01-07 17:41:51 UTC  
> Url: https://github.com/boostorg/locale/pull/144  

As per POSIX, see chapter 8.2, and linux man-pages, see man locale.7 the order should be  
  
- LC_ALL  
- LC_CTYPE  
- LANG   
  
This PR does this using the changes from #22

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-01-07 16:54:37 UTC  
> Url: https://github.com/boostorg/locale/pull/144#issuecomment-1374538988  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#144](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b593463) into [develop](https://codecov.io/gh/boostorg/locale/commit/343306a4a5c743d2cd470262ac7fc389327fb2bf?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (343306a) will **increase** coverage by `0.10%`.  
> The diff coverage is `91.11%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/144/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #144      +/-   ##  
===========================================  
+ Coverage    88.04%   88.15%   +0.10%       
===========================================  
  Files          104      106       +2       
  Lines         9181     9207      +26       
===========================================  
+ Hits          8083     8116      +33       
+ Misses        1098     1091       -7       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/format.hpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `94.32% <ø> (ø)` | |  
| [include/boost/locale/util.hpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `14.28% <ø> (ø)` | |  
| [src/boost/locale/shared/formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZm9ybWF0dGluZy5jcHA=) | `90.76% <0.00%> (ø)` | |  
| [test/test\_ios\_prop.cpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvc19wcm9wLmNwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/message.hpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `92.71% <50.00%> (ø)` | |  
| [src/boost/locale/util/default\_locale.cpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2RlZmF1bHRfbG9jYWxlLmNwcA==) | `92.59% <91.66%> (+24.73%)` | :arrow_up: |  
| [include/boost/locale/date\_time.hpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lLmhwcA==) | `93.98% <100.00%> (ø)` | |  
| [test/test\_helpers.cpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2hlbHBlcnMuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_util.cpp](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V0aWwuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [343306a...b593463](https://codecov.io/gh/boostorg/locale/pull/144?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
