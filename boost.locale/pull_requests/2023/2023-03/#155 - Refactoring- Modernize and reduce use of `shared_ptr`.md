# #155 Refactoring: Modernize and reduce use of `shared_ptr` [Closed]

> Username: Flamefire  
> Created at: 2023-03-08 17:41:13 UTC  
> Updated at: 2023-04-20 16:30:36 UTC  
> Closed at: 2023-04-20 16:30:35 UTC  
> Url: https://github.com/boostorg/locale/pull/155  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-09 05:12:03 UTC  
> Updated_at: 2023-04-08 02:29:22 UTC  
> Url: https://github.com/boostorg/locale/pull/155#issuecomment-1461289234  

## [Codecov](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#155](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (962f6ff) into [develop](https://codecov.io/gh/boostorg/locale/commit/a8e3a23c53ae8f59e298320d1b6a63be29fe1a12?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a8e3a23) will **increase** coverage by `0.00%`.  
> The diff coverage is `81.31%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/155/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #155   +/-   ##  
========================================  
  Coverage    89.44%   89.45%             
========================================  
  Files          109      110    +1       
  Lines         9457     9380   -77       
========================================  
- Hits          8459     8391   -68       
+ Misses         998      989    -9       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/boundary/segment.hpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvc2VnbWVudC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/format.hpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `94.32% <ø> (ø)` | |  
| [src/boost/locale/shared/mo\_lambda.hpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9fbGFtYmRhLmhwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_posix\_convert.cpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2NvbnZlcnQuY3Bw) | `81.57% <ø> (-2.15%)` | :arrow_down: |  
| [test/test\_std\_convert.cpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9jb252ZXJ0LmNwcA==) | `89.47% <ø> (-1.23%)` | :arrow_down: |  
| [test/test\_winapi\_convert.cpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9jb252ZXJ0LmNwcA==) | `89.47% <ø> (-0.78%)` | :arrow_down: |  
| [src/boost/locale/shared/mo\_lambda.cpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9fbGFtYmRhLmNwcA==) | `63.27% <62.76%> (+2.10%)` | :arrow_up: |  
| [src/boost/locale/win32/collate.cpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS93aW4zMi9jb2xsYXRlLmNwcA==) | `80.00% <66.66%> (ø)` | |  
| [src/boost/locale/shared/localization\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbG9jYWxpemF0aW9uX2JhY2tlbmQuY3Bw) | `73.77% <74.41%> (-1.02%)` | :arrow_down: |  
| [src/boost/locale/std/collate.cpp](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvY29sbGF0ZS5jcHA=) | `66.66% <80.00%> (ø)` | |  
| ... and [17 more](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a8e3a23...962f6ff](https://codecov.io/gh/boostorg/locale/pull/155?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
