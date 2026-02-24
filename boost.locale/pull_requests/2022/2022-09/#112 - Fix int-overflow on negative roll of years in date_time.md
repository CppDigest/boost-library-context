# #112 Fix int-overflow on negative roll of years in date_time [Merged]

> Username: Flamefire  
> Created at: 2022-09-14 20:18:59 UTC  
> Updated at: 2022-09-15 10:56:46 UTC  
> Merged at: 2022-09-15 10:44:22 UTC  
> Closed at: 2022-09-15 10:44:22 UTC  
> Url: https://github.com/boostorg/locale/pull/112  

For 64bit `time_t` the years could be in range [1, INT_MAX]  
For negative differences `addon` then was set to `INT_MAX` causing the expression `value - cur_min + difference + addon` to overflow for almost any `value` leading to undefined behavior.  
  
Fix this by rewriting the logic using a simpler approach and making assumptions clear using assertions.  
  
Also improve the test a lot to find and verify this.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-14 23:50:52 UTC  
> Url: https://github.com/boostorg/locale/pull/112#issuecomment-1247411122  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/112?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#112](https://codecov.io/gh/boostorg/locale/pull/112?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c3cdfe1) into [develop](https://codecov.io/gh/boostorg/locale/commit/6f183af9d30bfca86aecfebf83cc8b052d1e85b7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6f183af) will **increase** coverage by `1.35%`.  
> The diff coverage is `90.64%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/112/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/112?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #112      +/-   ##  
===========================================  
+ Coverage    80.73%   82.08%   +1.35%       
===========================================  
  Files           73       73                
  Lines         5766     5829      +63       
===========================================  
+ Hits          4655     4785     +130       
+ Misses        1111     1044      -67       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/112?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/localization\_backend.hpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbG9jYWxpemF0aW9uX2JhY2tlbmQuaHBw) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/shared/format.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZm9ybWF0LmNwcA==) | `0.00% <0.00%> (ø)` | |  
| [src/boost/locale/std/collate.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvY29sbGF0ZS5jcHA=) | `27.27% <0.00%> (ø)` | |  
| [test/test\_boundary.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2JvdW5kYXJ5LmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/posix/codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9jb2RlY3Z0LmNwcA==) | `84.42% <33.33%> (ø)` | |  
| [src/boost/locale/std/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvbnVtZXJpYy5jcHA=) | `58.20% <50.00%> (+1.13%)` | :arrow_up: |  
| [src/boost/locale/util/codecvt\_converter.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2NvZGVjdnRfY29udmVydGVyLmNwcA==) | `70.51% <50.00%> (-0.38%)` | :arrow_down: |  
| [test/test\_ios\_prop.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvc19wcm9wLmNwcA==) | `92.50% <50.00%> (ø)` | |  
| [src/boost/locale/shared/generator.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZ2VuZXJhdG9yLmNwcA==) | `79.12% <66.66%> (-0.45%)` | :arrow_down: |  
| [src/boost/locale/shared/date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZGF0ZV90aW1lLmNwcA==) | `82.96% <69.69%> (+16.73%)` | :arrow_up: |  
| ... and [34 more](https://codecov.io/gh/boostorg/locale/pull/112/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/112?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/112?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6f183af...c3cdfe1](https://codecov.io/gh/boostorg/locale/pull/112?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
