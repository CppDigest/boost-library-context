# #1071 Random search [Merged]

> Username: NAThompson  
> Created at: 2024-01-23 01:24:18 UTC  
> Updated at: 2024-02-19 00:59:43 UTC  
> Merged at: 2024-01-25 01:26:24 UTC  
> Closed at: 2024-01-25 01:26:24 UTC  
> Url: https://github.com/boostorg/math/pull/1071  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-24 18:05:19 UTC  
> Updated_at: 2024-01-25 01:18:44 UTC  
> Url: https://github.com/boostorg/math/pull/1071#issuecomment-1908661778  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `32 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`31b76ee`)](https://app.codecov.io/gh/boostorg/math/commit/31b76ee734ffd842b8dff5e8d6d7c0c2e5d2007d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.22% compared to head [(`34d5e13`)](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.22%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1071/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1071   +/-   ##  
========================================  
  Coverage    85.22%   85.22%             
========================================  
  Files          877      879    +2       
  Lines        66819    66899   +80       
========================================  
+ Hits         56948    57018   +70       
- Misses        9871     9881   +10       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/optimization/jso.hpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9qc28uaHBw) | `81.21% <100.00%> (ø)` | |  
| [test/cardinal\_cubic\_b\_spline\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jYXJkaW5hbF9jdWJpY19iX3NwbGluZV90ZXN0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/differential\_evolution\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9kaWZmZXJlbnRpYWxfZXZvbHV0aW9uX3Rlc3QuY3Bw) | `59.70% <100.00%> (ø)` | |  
| [test/fourier\_transform\_daubechies\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9mb3VyaWVyX3RyYW5zZm9ybV9kYXViZWNoaWVzX3Rlc3QuY3Bw) | `96.80% <ø> (-0.03%)` | :arrow_down: |  
| [test/gauss\_quadrature\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9nYXVzc19xdWFkcmF0dXJlX3Rlc3QuY3Bw) | `94.92% <100.00%> (+0.03%)` | :arrow_up: |  
| [test/random\_search\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9yYW5kb21fc2VhcmNoX3Rlc3QuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_functions\_for\_optimization.hpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Z1bmN0aW9uc19mb3Jfb3B0aW1pemF0aW9uLmhwcA==) | `100.00% <ø> (ø)` | |  
| [test/math\_unit\_test.hpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9tYXRoX3VuaXRfdGVzdC5ocHA=) | `24.00% <12.50%> (ø)` | |  
| [include/boost/math/optimization/random\_search.hpp](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9yYW5kb21fc2VhcmNoLmhwcA==) | `72.72% <72.72%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1071/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [31b76ee...34d5e13](https://app.codecov.io/gh/boostorg/math/pull/1071?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-02-18 08:22:58 UTC  
> Url: https://github.com/boostorg/math/pull/1071#issuecomment-1951007783  

Hi @NAThompson I just found a random instance of something random that looked very close to expectation and might somehow be related to some of this recent work.  
  
In a completely unrelated CI that had no association with this work, I think some runner found a random outlier [here](https://drone.cpp.al/boostorg/math/1777/240/2).

---

## Comment 3

> Username: NAThompson  
> Created_at: 2024-02-19 00:59:41 UTC  
> Url: https://github.com/boostorg/math/pull/1071#issuecomment-1951523558  

@ckormanyos : Bah, I thought I'd fixed that one. Hopefully fixed permanently [here](https://github.com/boostorg/math/pull/1097).

---
