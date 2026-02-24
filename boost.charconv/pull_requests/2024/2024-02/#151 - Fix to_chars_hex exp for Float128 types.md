# #151 Fix to_chars_hex exp for Float128 types [Merged]

> Username: mborland  
> Created at: 2024-02-02 08:46:57 UTC  
> Updated at: 2024-02-02 10:44:33 UTC  
> Merged at: 2024-02-02 10:44:30 UTC  
> Closed at: 2024-02-02 10:44:30 UTC  
> Url: https://github.com/boostorg/charconv/pull/151  

Closes: #149

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-02 10:42:19 UTC  
> Url: https://github.com/boostorg/charconv/pull/151#issuecomment-1923541339  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`8f5dde7`)](https://app.codecov.io/gh/cppalliance/charconv/commit/8f5dde7d20c07f8d6181378cc4914143164c464d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.53% compared to head [(`0716ed2`)](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.53%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/151/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #151   +/-   ##  
========================================  
  Coverage    88.53%   88.53%             
========================================  
  Files           59       59             
  Lines         8033     8033             
========================================  
  Hits          7112     7112             
  Misses         921      921             
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `96.25% <ø> (ø)` | |  
| [test/test\_float128.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [8f5dde7...0716ed2](https://app.codecov.io/gh/cppalliance/charconv/pull/151?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
