# #100 Refactor ICU build [Merged]

> Username: Flamefire  
> Created at: 2022-07-06 13:01:37 UTC  
> Updated at: 2022-07-07 13:00:18 UTC  
> Merged at: 2022-07-07 13:00:15 UTC  
> Closed at: 2022-07-07 13:00:15 UTC  
> Url: https://github.com/boostorg/locale/pull/100  

- Introduce and use `BOOST_LOCALE_ICU_VERSION`  
- Make sure headers include the config header first  
- Avoid INT8_MIN etc. macro redefinition warnings when using old ICU

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-07-07 12:57:44 UTC  
> Url: https://github.com/boostorg/locale/pull/100#issuecomment-1177572422  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/100?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#100](https://codecov.io/gh/boostorg/locale/pull/100?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fc42dea) into [develop](https://codecov.io/gh/boostorg/locale/commit/e1ab37923c48297d7a91bfb7f76e6690089fafe2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e1ab379) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #100   +/-   ##  
========================================  
  Coverage    80.97%   80.97%             
========================================  
  Files           76       76             
  Lines         5891     5891             
========================================  
  Hits          4770     4770             
  Misses        1121     1121             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/100?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/100/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `98.14% <ø> (ø)` | |  
| [test/test\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/100/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/100?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/100?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e1ab379...fc42dea](https://codecov.io/gh/boostorg/locale/pull/100?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
