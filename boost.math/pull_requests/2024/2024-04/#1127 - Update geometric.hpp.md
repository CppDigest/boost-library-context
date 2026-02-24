# #1127 Update geometric.hpp [Merged]

> Username: kenarab  
> Created at: 2024-04-29 18:26:56 UTC  
> Updated at: 2024-04-30 07:42:33 UTC  
> Merged at: 2024-04-30 07:42:33 UTC  
> Closed at: 2024-04-30 07:42:33 UTC  
> Url: https://github.com/boostorg/math/pull/1127  

The proposal is derived from this issue  
  
https://github.com/boostorg/math/issues/1126  
  
  
  
This line should be corrected  
At /math/distributions/geometric.hpp at line 404  
```  
  return std::log(p); // success_fraction  
        
```  
Now the code is  
```  
  return log(p); // success_fraction  
        
```  
  
  
When building that library. Throws this error  
  
```  
In file included from /opt/homebrew/include/boost/math/distributions.hpp:26:  
/opt/homebrew/include/boost/math/distributions/geometric.hpp:404:16: error: unexpected namespace name 'log': expected expression  
        return log(p); // success_fraction  
               ^  
1 error generated.  
make[2]: *** [CMakeFiles/PonisIqCoreNLV.dir/Unity/unity_0_cxx.cxx.o] Error 1  
make[1]: *** [CMakeFiles/PonisIqCoreNLV.dir/all] Error 2  
make: *** [all] Error 2  
```

---

## Review 1 [Changes requested]

> Username: mborland  
> Created_at: 2024-04-29 18:37:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/1127#pullrequestreview-2029296711  

`log` should be found by ADL for any `RealType`. The correct fix would be adding `using std::log;` under this line: https://github.com/boostorg/math/blob/develop/include/boost/math/distributions/geometric.hpp#L386. I think it also needs a `using std::exp;`.

---

## Review 2 [Approved]

> Username: mborland  
> Created_at: 2024-04-30 06:18:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1127#pullrequestreview-2030292059  

📁 include/boost/math/distributions/geometric.hpp

```diff
 385 |     { // Cumulative Distribution Function of geometric.
 386 |       using std::pow;
 387 |+       using std::log;
```

> Username: mborland  
> Created_at: 2024-04-30 06:17:30 UTC  
> Updated_at: 2024-04-30 06:18:29 UTC  
> Url: https://github.com/boostorg/math/pull/1127#discussion_r1584187254  

```diff  
-      using std::log;  
+      using std::log;  
+      using std::exp;  
```


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-04-30 07:15:35 UTC  
> Updated_at: 2024-04-30 07:36:19 UTC  
> Url: https://github.com/boostorg/math/pull/1127#issuecomment-2084549474  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1127?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.71%. Comparing base [(`28a5ffb`)](https://app.codecov.io/gh/boostorg/math/commit/28a5ffb1b498d2d15c76953b19cac1ed2108368a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1303786`)](https://app.codecov.io/gh/boostorg/math/pull/1127?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1127/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1127?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1127      +/-   ##  
===========================================  
+ Coverage    93.69%   93.71%   +0.01%       
===========================================  
  Files          770      770                
  Lines        61057    61057                
===========================================  
+ Hits         57209    57221      +12       
+ Misses        3848     3836      -12       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1127?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/geometric.hpp](https://app.codecov.io/gh/boostorg/math/pull/1127?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fgeometric.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZ2VvbWV0cmljLmhwcA==) | `89.28% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1127/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1127?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1127?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [28a5ffb...1303786](https://app.codecov.io/gh/boostorg/math/pull/1127?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: mborland  
> Created_at: 2024-04-30 07:42:27 UTC  
> Url: https://github.com/boostorg/math/pull/1127#issuecomment-2084608683  

macOS clang failures are being addressed elsewhere

---
