# #147 Move `BOOST_LOCALE_SOURCE` define to buildsystem [Merged]

> Username: Flamefire  
> Created at: 2023-02-16 17:16:02 UTC  
> Updated at: 2023-02-17 16:17:32 UTC  
> Merged at: 2023-02-17 16:17:29 UTC  
> Closed at: 2023-02-17 16:17:29 UTC  
> Url: https://github.com/boostorg/locale/pull/147  

Ensures that this is defined consistently for all files.  
  
Fixes #145

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-02-17 01:37:58 UTC  
> Updated_at: 2023-02-17 01:40:37 UTC  
> Url: https://github.com/boostorg/locale/pull/147#issuecomment-1433975446  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#147](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9b88ed6) into [develop](https://codecov.io/gh/boostorg/locale/commit/1652f11d94ae7803781c42891bdd91605e0ada8e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1652f11) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/147/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #147   +/-   ##  
========================================  
  Coverage    88.15%   88.15%             
========================================  
  Files          106      106             
  Lines         9207     9207             
========================================  
  Hits          8116     8116             
  Misses        1091     1091             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb2RlcGFnZS5jcHA=) | `79.31% <ø> (ø)` | |  
| [src/boost/locale/icu/boundary.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvYm91bmRhcnkuY3Bw) | `77.45% <ø> (ø)` | |  
| [src/boost/locale/icu/codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29kZWN2dC5jcHA=) | `77.96% <ø> (ø)` | |  
| [src/boost/locale/icu/collator.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29sbGF0b3IuY3Bw) | `91.17% <ø> (ø)` | |  
| [src/boost/locale/icu/conversion.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29udmVyc2lvbi5jcHA=) | `84.41% <ø> (ø)` | |  
| [src/boost/locale/icu/date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZGF0ZV90aW1lLmNwcA==) | `83.07% <ø> (ø)` | |  
| [src/boost/locale/icu/formatter.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `78.78% <ø> (ø)` | |  
| [src/boost/locale/icu/icu\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvaWN1X2JhY2tlbmQuY3Bw) | `82.35% <ø> (ø)` | |  
| [src/boost/locale/icu/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvbnVtZXJpYy5jcHA=) | `79.66% <ø> (ø)` | |  
| [src/boost/locale/icu/time\_zone.cpp](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvdGltZV96b25lLmNwcA==) | `100.00% <ø> (ø)` | |  
| ... and [27 more](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1652f11...9b88ed6](https://codecov.io/gh/boostorg/locale/pull/147?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
