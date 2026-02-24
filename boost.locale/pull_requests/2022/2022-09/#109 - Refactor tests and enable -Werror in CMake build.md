# #109 Refactor tests and enable -Werror in CMake build [Merged]

> Username: Flamefire  
> Created at: 2022-09-11 12:23:03 UTC  
> Updated at: 2022-09-13 18:31:56 UTC  
> Merged at: 2022-09-13 18:31:53 UTC  
> Closed at: 2022-09-13 18:31:53 UTC  
> Url: https://github.com/boostorg/locale/pull/109  

- Use `boost_test_jamfile` to automatically generate tests in CMake from the Jamfile  
- Add option to enable `-Werror` in CMake tests and use that  
- Add `TEST_EQ` to print values of failing equal-assertions  
- Unify and simplify code in tests

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-11 14:35:51 UTC  
> Updated_at: 2022-09-13 18:20:58 UTC  
> Url: https://github.com/boostorg/locale/pull/109#issuecomment-1242978417  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/109?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#109](https://codecov.io/gh/boostorg/locale/pull/109?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e4703df) into [develop](https://codecov.io/gh/boostorg/locale/commit/6f66770bb32141fffc13260f71aad206795e7cc8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6f66770) will **decrease** coverage by `0.28%`.  
> The diff coverage is `98.16%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/109/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/109?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #109      +/-   ##  
===========================================  
- Coverage    80.97%   80.69%   -0.29%       
===========================================  
  Files           76       73       -3       
  Lines         5891     5790     -101       
===========================================  
- Hits          4770     4672      -98       
+ Misses        1121     1118       -3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/109?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/date\_time.hpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lLmhwcA==) | `91.42% <ø> (ø)` | |  
| [test/test\_boundary.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2JvdW5kYXJ5LmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `92.35% <ø> (ø)` | |  
| [test/test\_codepage\_converter.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlX2NvbnZlcnRlci5jcHA=) | `99.38% <ø> (ø)` | |  
| [test/test\_collate.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbGxhdGUuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_config.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbmZpZy5jcHA=) | `96.36% <ø> (ø)` | |  
| [test/test\_convert.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbnZlcnQuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_generator.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_icu\_vs\_os\_timezone.cpp](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ljdV92c19vc190aW1lem9uZS5jcHA=) | `100.00% <ø> (ø)` | |  
| ... and [19 more](https://codecov.io/gh/boostorg/locale/pull/109/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/109?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/109?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6f66770...e4703df](https://codecov.io/gh/boostorg/locale/pull/109?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
