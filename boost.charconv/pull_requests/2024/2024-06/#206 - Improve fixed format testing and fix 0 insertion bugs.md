# #206 Improve fixed format testing and fix 0 insertion bugs [Merged]

> Username: mborland  
> Created at: 2024-06-20 20:11:21 UTC  
> Updated at: 2024-06-21 13:30:02 UTC  
> Merged at: 2024-06-21 13:29:59 UTC  
> Closed at: 2024-06-21 13:29:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/206  

Closes: #205

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-20 22:00:54 UTC  
> Updated_at: 2024-06-21 12:42:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/206#issuecomment-2181628797  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/206?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `94.18605%` with `5 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.52%. Comparing base [(`8e46c88`)](https://app.codecov.io/gh/boostorg/charconv/commit/8e46c8869f45494677487cfbdf0c164b7a5bdfea?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6391804`)](https://app.codecov.io/gh/boostorg/charconv/commit/6391804f5f4c18bd83add484c1423b4d00d9447c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/206/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/206?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #206      +/-   ##  
===========================================  
+ Coverage    94.01%   94.52%   +0.51%       
===========================================  
  Files           66       66                
  Lines         8439     8459      +20       
===========================================  
+ Hits          7934     7996      +62       
+ Misses         505      463      -42       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/206?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/charconv/detail/dragonbox/dragonbox.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/206?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fdragonbox%2Fdragonbox.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2RyYWdvbmJveC5ocHA=) | `97.67% <ø> (+0.30%)` | :arrow_up: |  
| [test/roundtrip.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/206?src=pr&el=tree&filepath=test%2Froundtrip.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9yb3VuZHRyaXAuY3Bw) | `98.34% <100.00%> (+0.05%)` | :arrow_up: |  
| [test/to\_chars\_float.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/206?src=pr&el=tree&filepath=test%2Fto_chars_float.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19mbG9hdC5jcHA=) | `99.66% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/to\_chars\_float\_STL\_comp.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/206?src=pr&el=tree&filepath=test%2Fto_chars_float_STL_comp.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19mbG9hdF9TVExfY29tcC5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/to\_chars\_sprintf.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/206?src=pr&el=tree&filepath=test%2Fto_chars_sprintf.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19zcHJpbnRmLmNwcA==) | `96.44% <ø> (-0.07%)` | :arrow_down: |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/206?src=pr&el=tree&filepath=src%2Fto_chars_float_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `85.71% <79.16%> (+3.80%)` | :arrow_up: |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/charconv/pull/206/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/206?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/206?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8e46c88...6391804](https://app.codecov.io/gh/boostorg/charconv/pull/206?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
