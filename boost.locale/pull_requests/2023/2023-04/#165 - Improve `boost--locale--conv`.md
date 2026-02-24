# #165 Improve `boost::locale::conv` [Merged]

> Username: Flamefire  
> Created at: 2023-04-30 17:33:26 UTC  
> Updated at: 2023-05-14 08:27:26 UTC  
> Merged at: 2023-05-13 14:10:44 UTC  
> Closed at: 2023-05-13 14:10:44 UTC  
> Url: https://github.com/boostorg/locale/pull/165  

Refactoring:  
  
- Improve/fix documentation and ordering of to_utf/from_utf functions  
- Take encoding by `const std::string&`  
- Fold exported function templates into a single definition & instantiation  
- Replace magic numbers  
- Make tests for `boost::locale::conv`-functions more readable/focused (e.g. by using better names) and add missing test cases  
  
Fixes:  
  
- Fix conversion to some codepages like iso-2022-jp on Windows

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-10 00:03:34 UTC  
> Updated_at: 2023-05-13 09:37:18 UTC  
> Url: https://github.com/boostorg/locale/pull/165#issuecomment-1541039041  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#165](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e7cac66) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/40f3201f31742b187f5043a0a4eab4d339790955?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (40f3201) will **increase** coverage by `0.63%`.  
> The diff coverage is `98.37%`.  
  
> :exclamation: Current head e7cac66 differs from pull request most recent head 3236f80. Consider uploading reports for the commit 3236f80 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/165/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #165      +/-   ##  
===========================================  
+ Coverage    90.82%   91.46%   +0.63%       
===========================================  
  Files          111      112       +1       
  Lines         9638     9798     +160       
===========================================  
+ Hits          8754     8962     +208       
+ Misses         884      836      -48       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/collator.hpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29sbGF0b3IuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/conversion.hpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/date\_time.hpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lLmhwcA==) | `93.98% <ø> (ø)` | |  
| [include/boost/locale/encoding\_utf.hpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmdfdXRmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/gnu\_gettext.hpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ251X2dldHRleHQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/util.hpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `14.28% <ø> (ø)` | |  
| [include/boost/locale/util/locale\_data.hpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC9sb2NhbGVfZGF0YS5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/boundary.cpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvYm91bmRhcnkuY3Bw) | `88.11% <0.00%> (ø)` | |  
| [src/boost/locale/icu/codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29kZWN2dC5jcHA=) | `77.96% <ø> (ø)` | |  
| [src/boost/locale/shared/date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZGF0ZV90aW1lLmNwcA==) | `83.94% <0.00%> (ø)` | |  
| ... and [28 more](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/165/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [40f3201...3236f80](https://app.codecov.io/gh/boostorg/locale/pull/165?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
