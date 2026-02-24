# #1158 Add intel compiler to CI [Open]

> Username: mborland  
> Created at: 2024-07-19 17:15:39 UTC  
> Updated at: 2024-07-22 13:27:47 UTC  
> Url: https://github.com/boostorg/math/pull/1158  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-19 19:24:39 UTC  
> Updated_at: 2024-07-20 04:15:26 UTC  
> Url: https://github.com/boostorg/math/pull/1158#issuecomment-2239976188  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1158?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.77%. Comparing base [(`5459e14`)](https://app.codecov.io/gh/boostorg/math/commit/5459e1485cf8fdbc917ac913023c0b7dbec26390?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`fba432a`)](https://app.codecov.io/gh/boostorg/math/commit/fba432ac6a0949eb1048d07da58ceb99a6654d36?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1158/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1158      +/-   ##  
===========================================  
- Coverage    93.77%   93.77%   -0.01%       
===========================================  
  Files          772      772                
  Lines        61352    61291      -61       
===========================================  
- Hits         57535    57474      -61       
  Misses        3817     3817                
```  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1158/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1158?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1158?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5459e14...fba432a](https://app.codecov.io/gh/boostorg/math/pull/1158?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-07-22 13:27:46 UTC  
> Url: https://github.com/boostorg/math/pull/1158#issuecomment-2242962649  

@jzmaddock and @NAThompson Now that this is running fine most of these intel failures just look like little tolerance adjustments. The hypergeometric testing has something horrible wrong going on with a number of gross errors or infinities.

---
