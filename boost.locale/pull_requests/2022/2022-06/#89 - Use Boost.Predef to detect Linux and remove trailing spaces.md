# #89 Use Boost.Predef to detect Linux and remove trailing spaces [Merged]

> Username: Flamefire  
> Created at: 2022-06-02 09:12:08 UTC  
> Updated at: 2022-06-04 09:50:42 UTC  
> Merged at: 2022-06-04 09:50:40 UTC  
> Closed at: 2022-06-04 09:50:40 UTC  
> Url: https://github.com/boostorg/locale/pull/89  

The `__linux` define may not be set. `__linux__` or `linux` mayb be used.  
So use Boost.Predef `BOOST_OS_LINUX` instead.  
Closes https://github.com/boostorg/locale/pull/49  
  
Removing the trailing spaces in a single commit avoids having git highlight them as possible issues on every commit.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-02 10:02:06 UTC  
> Updated_at: 2022-06-04 02:48:44 UTC  
> Url: https://github.com/boostorg/locale/pull/89#issuecomment-1144678293  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/89?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#89](https://codecov.io/gh/boostorg/locale/pull/89?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7677b1b) into [develop](https://codecov.io/gh/boostorg/locale/commit/cf016db2454aca5c421d772e911c18f1cca25c45?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cf016db) will **not change** coverage.  
> The diff coverage is `76.87%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #89   +/-   ##  
========================================  
  Coverage    79.92%   79.92%             
========================================  
  Files           76       76             
  Lines         5944     5944             
========================================  
  Hits          4751     4751             
  Misses        1193     1193             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/89?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/conversion.hpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/generator.hpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJhdG9yLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/localization\_backend.hpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbG9jYWxpemF0aW9uX2JhY2tlbmQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/util.hpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `0.00% <0.00%> (ø)` | |  
| [src/encoding/codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2VuY29kaW5nL2NvZGVwYWdlLmNwcA==) | `92.50% <ø> (ø)` | |  
| [src/encoding/conv.hpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2VuY29kaW5nL2NvbnYuaHBw) | `88.88% <ø> (ø)` | |  
| [src/posix/codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3Bvc2l4L2NvZGVjdnQuY3Bw) | `84.42% <ø> (ø)` | |  
| [src/shared/format.cpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9mb3JtYXQuY3Bw) | `0.00% <0.00%> (ø)` | |  
| [src/shared/ids.cpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9pZHMuY3Bw) | `90.00% <ø> (ø)` | |  
| [src/shared/mo\_lambda.hpp](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9tb19sYW1iZGEuaHBw) | `100.00% <ø> (ø)` | |  
| ... and [63 more](https://codecov.io/gh/boostorg/locale/pull/89/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/89?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/89?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cf016db...7677b1b](https://codecov.io/gh/boostorg/locale/pull/89?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
