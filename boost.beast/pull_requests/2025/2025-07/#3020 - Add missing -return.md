# #3020 Add missing "return" [Closed]

> Username: DockedFerret800  
> Created at: 2025-07-23 21:55:14 UTC  
> Updated at: 2025-07-29 18:23:30 UTC  
> Closed at: 2025-07-24 11:57:03 UTC  
> Url: https://github.com/boostorg/beast/pull/3020  

Hi. Aren't we supposed to return in case of an error? Similarly to what we do in other examples, for example in `websocket_server_async_local.cpp`.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-07-23 23:13:43 UTC  
> Updated_at: 2025-07-24 04:51:34 UTC  
> Url: https://github.com/boostorg/beast/pull/3020#issuecomment-3111084535  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3020?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.09%. Comparing base [(`1fc5512`)](https://app.codecov.io/gh/boostorg/beast/commit/1fc5512b92ca1ecc7d30a220913cafcb8a9c443f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`415bf82`)](https://app.codecov.io/gh/boostorg/beast/commit/415bf825248ab6e349c40813592ca0df97e12f34?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3020/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3020?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #3020      +/-   ##  
===========================================  
- Coverage    93.11%   93.09%   -0.03%       
===========================================  
  Files          176      176                
  Lines        13647    13647                
===========================================  
- Hits         12708    12704       -4       
- Misses         939      943       +4       
```  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/3020/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3020?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3020?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1fc5512...415bf82](https://app.codecov.io/gh/boostorg/beast/pull/3020?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ashtum  
> Created_at: 2025-07-24 04:56:57 UTC  
> Url: https://github.com/boostorg/beast/pull/3020#issuecomment-3111962241  

@DockedFerret800, thank you for the PR. There are similar issues in a few other examples as well. Could you please go ahead and fix those as part of this commit?

---

## Comment 3

> Username: DockedFerret800  
> Created_at: 2025-07-24 11:13:55 UTC  
> Url: https://github.com/boostorg/beast/pull/3020#issuecomment-3113059922  

> @DockedFerret800, thank you for the PR. There are similar issues in a few other examples as well. Could you please go ahead and fix those as part of this commit?  
  
Sure

---

## Comment 4

> Username: DockedFerret800  
> Created_at: 2025-07-24 11:43:28 UTC  
> Url: https://github.com/boostorg/beast/pull/3020#issuecomment-3113138420  

I think these are all I was able to find.

---

## Comment 5

> Username: ashtum  
> Created_at: 2025-07-24 11:52:50 UTC  
> Url: https://github.com/boostorg/beast/pull/3020#issuecomment-3113165827  

Thanks, please squash them into a single commit if possible.

---

## Comment 6

> Username: DockedFerret800  
> Created_at: 2025-07-24 11:57:03 UTC  
> Url: https://github.com/boostorg/beast/pull/3020#issuecomment-3113176943  

I'll recreate a PR.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2025-07-29 17:43:41 UTC  
> Url: https://github.com/boostorg/beast/pull/3020#issuecomment-3133461253  

Commit messages are even better when they start with nouns instead of verbs:  
  
"examples use return statements where needed"

---

## Comment 8

> Username: DockedFerret800  
> Created_at: 2025-07-29 18:23:30 UTC  
> Url: https://github.com/boostorg/beast/pull/3020#issuecomment-3133584693  

> Commit messages are even better when they start with nouns instead of verbs:  
>   
> "examples use return statements where needed"  
  
I'll remember that for the future. Thank you!

---
