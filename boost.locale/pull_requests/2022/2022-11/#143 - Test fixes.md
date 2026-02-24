# #143 Test fixes [Merged]

> Username: Flamefire  
> Created at: 2022-11-26 10:16:40 UTC  
> Updated at: 2022-11-27 09:25:13 UTC  
> Merged at: 2022-11-27 09:25:10 UTC  
> Closed at: 2022-11-27 09:25:10 UTC  
> Url: https://github.com/boostorg/locale/pull/143  

Fix spurious test failures especially for parallel builds via B2 with multiple variants.  
  
This is caused by access to files with fixed names, so different variants of the same test or even different tests using the same filename may interfere with each other.  
  
Also fix a "constant condition" warning in `utf_name()` and a bit of refactoring to make the tests easier to read (almost only renaming with no actual change to behavior)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-26 12:42:39 UTC  
> Updated_at: 2022-11-26 19:29:00 UTC  
> Url: https://github.com/boostorg/locale/pull/143#issuecomment-1328039760  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/143?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#143](https://codecov.io/gh/boostorg/locale/pull/143?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c34e0d5) into [master](https://codecov.io/gh/boostorg/locale/commit/1558586b4dc9b07b42fbe6c701af0f5fd0ef1780?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1558586) will **increase** coverage by `0.03%`.  
> The diff coverage is `97.36%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/143/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/143?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #143      +/-   ##  
==========================================  
+ Coverage   88.00%   88.04%   +0.03%       
==========================================  
  Files         104      104                
  Lines        9154     9181      +27       
==========================================  
+ Hits         8056     8083      +27       
  Misses       1098     1098                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/143?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/conv.hpp](https://codecov.io/gh/boostorg/locale/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb252LmhwcA==) | `92.30% <88.88%> (+3.41%)` | :arrow_up: |  
| [test/test\_codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlLmNwcA==) | `95.23% <98.50%> (+0.37%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/143?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/143?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1558586...c34e0d5](https://codecov.io/gh/boostorg/locale/pull/143?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
