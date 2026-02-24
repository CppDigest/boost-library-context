# #167 Localization backend manager improvement [Merged]

> Username: Flamefire  
> Created at: 2023-05-03 19:03:06 UTC  
> Updated at: 2023-05-04 16:32:40 UTC  
> Merged at: 2023-05-04 16:32:37 UTC  
> Closed at: 2023-05-04 16:32:37 UTC  
> Url: https://github.com/boostorg/locale/pull/167  

- Add missing move ctor/assign to `localization_backend_manager` to be used by e.g. `localization_backend_manager::global()`  
- check and correct missing `noexcept` in move ops  
- Add test for `localization_backend_manager::get_all_backends`

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-04 03:58:31 UTC  
> Updated_at: 2023-05-04 05:59:39 UTC  
> Url: https://github.com/boostorg/locale/pull/167#issuecomment-1534064079  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#167](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3a4c6ea) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/0daace933ca41a32763735b570bc1429cdbbb935?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0daace9) will **increase** coverage by `0.05%`.  
> The diff coverage is `97.36%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/167/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #167      +/-   ##  
===========================================  
+ Coverage    90.69%   90.74%   +0.05%       
===========================================  
  Files          111      111                
  Lines         9654     9642      -12       
===========================================  
- Hits          8756     8750       -6       
+ Misses         898      892       -6       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/localization\_backend.hpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbG9jYWxpemF0aW9uX2JhY2tlbmQuaHBw) | `50.00% <ø> (ø)` | |  
| [src/boost/locale/encoding/codepage.cpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb2RlcGFnZS5jcHA=) | `93.47% <ø> (ø)` | |  
| [src/boost/locale/shared/localization\_backend.cpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbG9jYWxpemF0aW9uX2JhY2tlbmQuY3Bw) | `78.15% <94.11%> (+4.38%)` | :arrow_up: |  
| [include/boost/locale/hold\_ptr.hpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvaG9sZF9wdHIuaHBw) | `95.65% <100.00%> (ø)` | |  
| [include/boost/locale/message.hpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_codepage.cpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlLmNwcA==) | `95.39% <100.00%> (-0.08%)` | :arrow_down: |  
| [test/test\_date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `98.28% <100.00%> (-0.02%)` | :arrow_down: |  
| [test/test\_generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `99.27% <100.00%> (+0.02%)` | :arrow_up: |  
| [test/test\_message.cpp](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X21lc3NhZ2UuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0daace9...3a4c6ea](https://app.codecov.io/gh/boostorg/locale/pull/167?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
