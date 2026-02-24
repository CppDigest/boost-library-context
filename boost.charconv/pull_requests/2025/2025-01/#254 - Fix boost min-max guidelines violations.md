# #254 Fix boost min/max guidelines violations [Merged]

> Username: mborland  
> Created at: 2025-01-03 18:26:35 UTC  
> Updated at: 2025-01-03 20:46:39 UTC  
> Merged at: 2025-01-03 20:45:53 UTC  
> Closed at: 2025-01-03 20:45:53 UTC  
> Url: https://github.com/boostorg/charconv/pull/254  

Closes: #251

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-03 20:17:18 UTC  
> Updated_at: 2025-01-03 20:46:39 UTC  
> Url: https://github.com/boostorg/charconv/pull/254#issuecomment-2569764929  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/254?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.85%. Comparing base [(`25dfbce`)](https://app.codecov.io/gh/boostorg/charconv/commit/25dfbce0c904432d44cffbc91cb64f12b720d0ef?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e52759e`)](https://app.codecov.io/gh/boostorg/charconv/commit/e52759e589e8f2d5843d05b8a62df389527ad8e9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/254/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #254   +/-   ##  
========================================  
  Coverage    94.85%   94.85%             
========================================  
  Files           69       69             
  Lines         9077     9077             
========================================  
  Hits          8610     8610             
  Misses         467      467             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/254?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/emulated128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Femulated128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZW11bGF0ZWQxMjguaHBw) | `100.00% <ø> (ø)` | |  
| [...nclude/boost/charconv/detail/fallback\_routines.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Ffallback_routines.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZmFsbGJhY2tfcm91dGluZXMuaHBw) | `84.61% <ø> (ø)` | |  
| [.../boost/charconv/detail/fast\_float/parse\_number.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Ffast_float%2Fparse_number.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZmFzdF9mbG9hdC9wYXJzZV9udW1iZXIuaHBw) | `92.18% <ø> (ø)` | |  
| [src/from\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=src%2Ffrom_chars_float_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Zyb21fY2hhcnNfZmxvYXRfaW1wbC5ocHA=) | `82.97% <ø> (ø)` | |  
| [test/from\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=test%2Ffrom_chars.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9mcm9tX2NoYXJzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/limits.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=test%2Flimits.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9saW1pdHMuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/roundtrip.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=test%2Froundtrip.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9yb3VuZHRyaXAuY3Bw) | `98.75% <100.00%> (ø)` | |  
| [test/test\_128bit\_emulation.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=test%2Ftest_128bit_emulation.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0XzEyOGJpdF9lbXVsYXRpb24uY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_128bit\_native.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=test%2Ftest_128bit_native.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0XzEyOGJpdF9uYXRpdmUuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_parser.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree&filepath=test%2Ftest_parser.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3BhcnNlci5jcHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [2 more](https://app.codecov.io/gh/boostorg/charconv/pull/254?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/254?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/254?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [25dfbce...e52759e](https://app.codecov.io/gh/boostorg/charconv/pull/254?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
