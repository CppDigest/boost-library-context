# #189 Fix first character `from_chars` integer pattern matching [Merged]

> Username: mborland  
> Created at: 2024-04-22 08:01:34 UTC  
> Updated at: 2024-04-23 06:18:03 UTC  
> Merged at: 2024-04-23 06:17:59 UTC  
> Closed at: 2024-04-23 06:17:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/189  

Closes: #188

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-04-22 10:16:19 UTC  
> Updated_at: 2024-04-22 10:27:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/189#issuecomment-2069018098  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/189?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.39%. Comparing base [(`100473b`)](https://app.codecov.io/gh/boostorg/charconv/commit/100473b1dc3c3dfff0513e584c8f447824c7e34c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6bab2a7`)](https://app.codecov.io/gh/boostorg/charconv/pull/189?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/189/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/189?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #189   +/-   ##  
========================================  
  Coverage    93.38%   93.39%             
========================================  
  Files           66       66             
  Lines         8420     8432   +12       
========================================  
+ Hits          7863     7875   +12       
  Misses         557      557             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/189?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/charconv/detail/from\_chars\_integer\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/189?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Ffrom_chars_integer_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZnJvbV9jaGFyc19pbnRlZ2VyX2ltcGwuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/from\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/189?src=pr&el=tree&filepath=test%2Ffrom_chars.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9mcm9tX2NoYXJzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/quick.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/189?src=pr&el=tree&filepath=test%2Fquick.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9xdWljay5jcHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/189?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/189?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [100473b...6bab2a7](https://app.codecov.io/gh/boostorg/charconv/pull/189?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
