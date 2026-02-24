# #873 Fix -Wdeprecated-declarations for sprintf [Merged]

> Username: mborland  
> Created at: 2023-03-21 01:51:55 UTC  
> Updated at: 2023-03-25 13:26:34 UTC  
> Merged at: 2023-03-25 13:24:34 UTC  
> Closed at: 2023-03-25 13:24:34 UTC  
> Url: https://github.com/boostorg/json/pull/873  

Clang issues `-Wdeprecated-declarations` warning for use of `sprintf`:  
  
```  
double.cpp:35:10: warning: 'sprintf' is deprecated: This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead. [-Wdeprecated-declarations]  
    std::sprintf(buf, format,  
         ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/stdio.h:188:1: note: 'sprintf' has been explicitly marked deprecated here  
__deprecated_msg("This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead.")  
^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/sys/cdefs.h:215:48: note: expanded from macro '__deprecated_msg'  
        #define __deprecated_msg(_msg) __attribute__((__deprecated__(_msg)))  
```  
  
Since we know the size simply replace `sprintf` with `snprintf`.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-21 02:08:20 UTC  
> Updated_at: 2023-03-21 02:09:01 UTC  
> Url: https://github.com/boostorg/json/pull/873#issuecomment-1477182125  

## [Codecov](https://codecov.io/gh/boostorg/json/pull/873?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#873](https://codecov.io/gh/boostorg/json/pull/873?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d79336b) into [develop](https://codecov.io/gh/boostorg/json/commit/4d4d5fc14e25f4eb40dd091edb66656383d26fd6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4d4d5fc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/873/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/873?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #873      +/-   ##  
===========================================  
- Coverage    99.19%   99.19%   -0.01%       
===========================================  
  Files           70       70                
  Lines         6853     6851       -2       
===========================================  
- Hits          6798     6796       -2       
  Misses          55       55                
```  
  
  
[see 1 file with indirect coverage changes](https://codecov.io/gh/boostorg/json/pull/873/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/json/pull/873?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/873?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4d4d5fc...d79336b](https://codecov.io/gh/boostorg/json/pull/873?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-03-21 03:04:43 UTC  
> Url: https://github.com/boostorg/json/pull/873#issuecomment-1477219232  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/873/pullrequest-condensed-d2227a3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/873/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/873/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-03-25 13:26:34 UTC  
> Url: https://github.com/boostorg/json/pull/873#issuecomment-1483823036  

Thanks for the fix

---
