# #83 Fix compilation on MinGW platforms [Merged]

> Username: Flamefire  
> Created at: 2022-05-28 07:56:55 UTC  
> Updated at: 2022-05-28 10:40:59 UTC  
> Merged at: 2022-05-28 10:40:57 UTC  
> Closed at: 2022-05-28 10:40:57 UTC  
> Url: https://github.com/boostorg/locale/pull/83  

Compiling with a specific C++ standard w/o GNU extensions, e.g. --std=c++11, removes the required _wfopen function.  
So disable the strict-mode on MinGW but be careful not to do so on MinGW-w64 where this would be an error.  
  
Requires:  
- [x] #82

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-28 09:24:27 UTC  
> Updated_at: 2022-05-28 10:07:32 UTC  
> Url: https://github.com/boostorg/locale/pull/83#issuecomment-1140220484  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/83?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#83](https://codecov.io/gh/boostorg/locale/pull/83?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c25ddb5) into [develop](https://codecov.io/gh/boostorg/locale/commit/2ee5ea7dac6b27ba46b5565fadc1e78b3c99a342?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2ee5ea7) will **decrease** coverage by `0.09%`.  
> The diff coverage is `74.01%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #83      +/-   ##  
===========================================  
- Coverage    80.04%   79.94%   -0.10%       
===========================================  
  Files           76       76                
  Lines         5959     5930      -29       
===========================================  
- Hits          4770     4741      -29       
  Misses        1189     1189                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/83?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/util.hpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `0.00% <ø> (ø)` | |  
| [src/std/collate.cpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3N0ZC9jb2xsYXRlLmNwcA==) | `27.27% <0.00%> (ø)` | |  
| [src/util/numeric.hpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3V0aWwvbnVtZXJpYy5ocHA=) | `76.10% <20.00%> (ø)` | |  
| [src/shared/localization\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9sb2NhbGl6YXRpb25fYmFja2VuZC5jcHA=) | `70.22% <50.00%> (ø)` | |  
| [src/shared/mo\_lambda.cpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9tb19sYW1iZGEuY3Bw) | `60.34% <50.00%> (ø)` | |  
| [src/posix/posix\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3Bvc2l4L3Bvc2l4X2JhY2tlbmQuY3Bw) | `88.88% <75.00%> (ø)` | |  
| [src/std/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3N0ZC9udW1lcmljLmNwcA==) | `57.07% <75.00%> (ø)` | |  
| [src/std/std\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3N0ZC9zdGRfYmFja2VuZC5jcHA=) | `85.39% <75.00%> (ø)` | |  
| [src/util/codecvt\_converter.cpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3V0aWwvY29kZWN2dF9jb252ZXJ0ZXIuY3Bw) | `71.42% <80.00%> (-0.51%)` | :arrow_down: |  
| [src/util/gregorian.cpp](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3V0aWwvZ3JlZ29yaWFuLmNwcA==) | `64.44% <80.00%> (-0.24%)` | :arrow_down: |  
| ... and [14 more](https://codecov.io/gh/boostorg/locale/pull/83/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/83?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/83?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [90dc1ae...c25ddb5](https://codecov.io/gh/boostorg/locale/pull/83?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
