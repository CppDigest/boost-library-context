# #127 Add more windows code pages [Merged]

> Username: Flamefire  
> Created at: 2022-10-22 16:34:28 UTC  
> Updated at: 2022-10-24 07:38:33 UTC  
> Merged at: 2022-10-23 12:11:32 UTC  
> Closed at: 2022-10-23 12:11:32 UTC  
> Url: https://github.com/boostorg/locale/pull/127  

Codepages used from https://docs.microsoft.com/en-us/windows/desktop/intl/code-page-identifiers as added in #39  
  
Also added tests to make sure the list is correctly sorted and unique. Remove the duplicates.  
  
Finally handle the case of multiple codepages with the same name

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-23 01:35:22 UTC  
> Url: https://github.com/boostorg/locale/pull/127#issuecomment-1287972128  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/127?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#127](https://codecov.io/gh/boostorg/locale/pull/127?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a3b1982) into [develop](https://codecov.io/gh/boostorg/locale/commit/cc0337c6a93d2637d8157269a59912f68f8db3b4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cc0337c) will **increase** coverage by `0.00%`.  
> The diff coverage is `89.28%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/127/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/127?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #127   +/-   ##  
========================================  
  Coverage    88.45%   88.46%             
========================================  
  Files          100      101    +1       
  Lines         8977     8996   +19       
========================================  
+ Hits          7941     7958   +17       
- Misses        1036     1038    +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/127?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/conv.hpp](https://codecov.io/gh/boostorg/locale/pull/127/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb252LmhwcA==) | `88.88% <ø> (ø)` | |  
| [src/boost/locale/encoding/wconv\_codepage.ipp](https://codecov.io/gh/boostorg/locale/pull/127/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy93Y29udl9jb2RlcGFnZS5pcHA=) | `79.09% <85.71%> (-0.24%)` | :arrow_down: |  
| [test/test\_codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/127/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlLmNwcA==) | `94.30% <89.47%> (-0.36%)` | :arrow_down: |  
| [src/boost/locale/encoding/win\_codepages.hpp](https://codecov.io/gh/boostorg/locale/pull/127/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy93aW5fY29kZXBhZ2VzLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/127?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/127?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cc0337c...a3b1982](https://codecov.io/gh/boostorg/locale/pull/127?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
