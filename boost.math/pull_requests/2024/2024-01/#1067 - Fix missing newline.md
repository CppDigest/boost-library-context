# #1067 Fix missing newline. [Merged]

> Username: jzmaddock  
> Created at: 2024-01-21 10:27:26 UTC  
> Updated at: 2024-01-21 19:47:54 UTC  
> Merged at: 2024-01-21 12:14:49 UTC  
> Closed at: 2024-01-21 12:14:49 UTC  
> Url: https://github.com/boostorg/math/pull/1067  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-21 11:29:24 UTC  
> Updated_at: 2024-01-21 12:09:31 UTC  
> Url: https://github.com/boostorg/math/pull/1067#issuecomment-1902599824  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1067?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`6f81305`)](https://app.codecov.io/gh/boostorg/math/commit/6f81305aa61cad7038d23337e5d89e87bddd3c94?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.22% compared to head [(`9f5e81d`)](https://app.codecov.io/gh/boostorg/math/pull/1067?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 85.24%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1067/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1067?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1067      +/-   ##  
===========================================  
+ Coverage    85.22%   85.24%   +0.01%       
===========================================  
  Files          875      875                
  Lines        66568    66568                
===========================================  
+ Hits         56733    56745      +12       
+ Misses        9835     9823      -12       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1067?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/optimization/detail/common.hpp](https://app.codecov.io/gh/boostorg/math/pull/1067?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9kZXRhaWwvY29tbW9uLmhwcA==) | `26.96% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1067/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1067?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1067?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6f81305...9f5e81d](https://app.codecov.io/gh/boostorg/math/pull/1067?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: NAThompson  
> Created_at: 2024-01-21 18:32:48 UTC  
> Url: https://github.com/boostorg/math/pull/1067#issuecomment-1902721829  

Thanks @jzmaddock ! Quick question: Do you have a `.clang-format` you like so we can automate some of these problems away?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-01-21 19:47:53 UTC  
> Url: https://github.com/boostorg/math/pull/1067#issuecomment-1902741501  

>Quick question: Do you have a .clang-format you like so we can automate some of these problems away?  
  
Sorry no, I have found it can make a right mess of things if you're not careful, haven't tried it recently though...

---
