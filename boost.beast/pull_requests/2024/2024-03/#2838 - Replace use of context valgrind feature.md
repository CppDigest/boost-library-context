# #2838 Replace use of context valgrind feature. [Merged]

> Username: grafikrobot  
> Created at: 2024-03-17 21:19:40 UTC  
> Updated at: 2024-03-18 12:00:24 UTC  
> Merged at: 2024-03-18 07:01:42 UTC  
> Closed at: 2024-03-18 07:01:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2838  

Declare our own b2 feature for valgrind and adding the BOOST_USE_VALGRIND predef. This avoids dependency ordering problems since Beast doesn't depend on Boost.Context. Which is the lib that defines the other valgrind feature.  
  
This change is needed to avoid build errors for this PR https://github.com/boostorg/boost/pull/854

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-03-17 21:37:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2838#issuecomment-2002625464  

PS. This is safe to merge to develop and master.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-03-17 22:15:44 UTC  
> Updated_at: 2024-03-17 22:39:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2838#issuecomment-2002635565  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2838?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.03%. Comparing base [(`c9afa3b`)](https://app.codecov.io/gh/boostorg/beast/commit/c9afa3bbbce8c99104397ff2695e31bafe3c1725?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`bdbb196`)](https://app.codecov.io/gh/boostorg/beast/pull/2838?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2838/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2838?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2838      +/-   ##  
===========================================  
- Coverage    93.06%   93.03%   -0.03%       
===========================================  
  Files          178      178                
  Lines        13687    13687                
===========================================  
- Hits         12738    12734       -4       
- Misses         949      953       +4       
```  
  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2838/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2838?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2838?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c9afa3b...bdbb196](https://app.codecov.io/gh/boostorg/beast/pull/2838?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
