# #86 Handle warnings [Merged]

> Username: Flamefire  
> Created at: 2022-05-29 20:15:49 UTC  
> Updated at: 2022-05-31 08:28:42 UTC  
> Merged at: 2022-05-31 08:28:38 UTC  
> Closed at: 2022-05-31 08:28:39 UTC  
> Url: https://github.com/boostorg/locale/pull/86  

This makes the default build warning free. (Tested on Windows MSVC & MSYS)  
  
Reduces the log clutter which helps finding actual errors.  
  
- Rename facet-id init and avoid unused-result warning, closes #75  
- Add `_CRT_SECURE_NO_WARNINGS` to build rules  
- Handle some possible (although highly unlikely) int overflows by throwing or returning an error

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-29 20:31:21 UTC  
> Updated_at: 2022-05-30 15:47:52 UTC  
> Url: https://github.com/boostorg/locale/pull/86#issuecomment-1140519959  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/86?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#86](https://codecov.io/gh/boostorg/locale/pull/86?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (039f016) into [develop](https://codecov.io/gh/boostorg/locale/commit/5a43f604453ae0e2cb1c4b320b1adee4ff76421e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5a43f60) will **decrease** coverage by `0.06%`.  
> The diff coverage is `98.24%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #86      +/-   ##  
===========================================  
- Coverage    79.88%   79.81%   -0.07%       
===========================================  
  Files           76       76                
  Lines         5921     5911      -10       
===========================================  
- Hits          4730     4718      -12       
- Misses        1191     1193       +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/86?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/shared/mo\_lambda.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9tb19sYW1iZGEuY3Bw) | `60.34% <ø> (ø)` | |  
| [src/std/collate.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3N0ZC9jb2xsYXRlLmNwcA==) | `27.27% <ø> (ø)` | |  
| [src/util/gregorian.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3V0aWwvZ3JlZ29yaWFuLmNwcA==) | `64.03% <ø> (-0.29%)` | :arrow_down: |  
| [test/test\_config.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbmZpZy5jcHA=) | `89.83% <ø> (ø)` | |  
| [test/test\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_ios\_prop.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvc19wcm9wLmNwcA==) | `86.04% <ø> (ø)` | |  
| [test/test\_utf.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V0Zi5jcHA=) | `98.18% <ø> (-0.17%)` | :arrow_down: |  
| [test/test\_winapi\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9mb3JtYXR0aW5nLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_message.cpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X21lc3NhZ2UuY3Bw) | `95.54% <50.00%> (ø)` | |  
| [include/boost/locale/generic\_codecvt.hpp](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJpY19jb2RlY3Z0LmhwcA==) | `97.14% <100.00%> (ø)` | |  
| ... and [9 more](https://codecov.io/gh/boostorg/locale/pull/86/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/86?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/86?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5a43f60...039f016](https://codecov.io/gh/boostorg/locale/pull/86?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
