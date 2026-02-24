# #255 Remove superflous subfolders of `src` [Merged]

> Username: Flamefire  
> Created at: 2025-01-09 18:17:27 UTC  
> Updated at: 2025-01-10 11:50:52 UTC  
> Merged at: 2025-01-10 11:50:45 UTC  
> Closed at: 2025-01-10 11:50:45 UTC  
> Url: https://github.com/boostorg/locale/pull/255  

The shorter paths make the sources easier to navigate.  
  
Basically revert of #99 / f44b3bb771f01fb94614260d57e1d09cdfe1cdff after the underlying issue is resolved.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-10 10:23:57 UTC  
> Updated_at: 2025-01-10 11:50:52 UTC  
> Url: https://github.com/boostorg/locale/pull/255#issuecomment-2582304069  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/255?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.87%. Comparing base [(`abd3b98`)](https://app.codecov.io/gh/boostorg/locale/commit/abd3b983b86efdcb5b8aee0c1ae4861f09d4ffbe?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`59fa713`)](https://app.codecov.io/gh/boostorg/locale/commit/59fa7139691f2ff174b05763fc85198020a6fc33?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/255/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #255      +/-   ##  
===========================================  
+ Coverage    95.72%   95.87%   +0.15%       
===========================================  
  Files          117      117                
  Lines        10386    10406      +20       
===========================================  
+ Hits          9942     9977      +35       
+ Misses         444      429      -15       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/255?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/encoding/codepage.cpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Fencoding%2Fcodepage.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2VuY29kaW5nL2NvZGVwYWdlLmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/encoding/iconv\_converter.hpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Fencoding%2Ficonv_converter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2VuY29kaW5nL2ljb252X2NvbnZlcnRlci5ocHA=) | `94.64% <ø> (ø)` | |  
| [src/encoding/uconv\_converter.hpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Fencoding%2Fuconv_converter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2VuY29kaW5nL3Vjb252X2NvbnZlcnRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/encoding/wconv\_converter.hpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Fencoding%2Fwconv_converter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2VuY29kaW5nL3djb252X2NvbnZlcnRlci5ocHA=) | `91.61% <ø> (ø)` | |  
| [src/icu/boundary.cpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Ficu%2Fboundary.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9ib3VuZGFyeS5jcHA=) | `93.81% <ø> (ø)` | |  
| [src/icu/cdata.hpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Ficu%2Fcdata.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9jZGF0YS5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/icu/codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Ficu%2Fcodecvt.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9jb2RlY3Z0LmNwcA==) | `84.09% <ø> (ø)` | |  
| [src/icu/collator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Ficu%2Fcollator.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9jb2xsYXRvci5jcHA=) | `94.20% <ø> (ø)` | |  
| [src/icu/conversion.cpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Ficu%2Fconversion.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9jb252ZXJzaW9uLmNwcA==) | `93.33% <ø> (ø)` | |  
| [src/icu/date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree&filepath=src%2Ficu%2Fdate_time.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9kYXRlX3RpbWUuY3Bw) | `92.85% <ø> (ø)` | |  
| ... and [57 more](https://app.codecov.io/gh/boostorg/locale/pull/255?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [4 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/255/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/255?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/255?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [abd3b98...59fa713](https://app.codecov.io/gh/boostorg/locale/pull/255?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
