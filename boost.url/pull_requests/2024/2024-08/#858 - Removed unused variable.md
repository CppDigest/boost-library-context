# #858 Removed unused variable [Merged]

> Username: BrianWeed  
> Created at: 2024-08-15 17:23:53 UTC  
> Updated at: 2024-08-16 16:52:58 UTC  
> Merged at: 2024-08-16 16:18:31 UTC  
> Closed at: 2024-08-16 16:18:31 UTC  
> Url: https://github.com/boostorg/url/pull/858  

'buf' is unused here

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-08-15 17:50:10 UTC  
> Url: https://github.com/boostorg/url/pull/858#issuecomment-2291850284  

GCOVR code coverage report [https://858.url.prtest2.cppalliance.org/gcovr/index.html](https://858.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://858.url.prtest2.cppalliance.org/genhtml/index.html](https://858.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://858.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://858.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-08-15 17:50:30 UTC  
> Updated_at: 2024-08-16 16:18:53 UTC  
> Url: https://github.com/boostorg/url/pull/858#issuecomment-2291850840  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/858?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.21%. Comparing base [(`094a535`)](https://app.codecov.io/gh/boostorg/url/commit/094a535b9365a6c88b87addf179f1616f22b7a2c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8789cf4`)](https://app.codecov.io/gh/boostorg/url/commit/8789cf41d28b420561da827080cf0e5c5ff71c20?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/858/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/858?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #858      +/-   ##  
===========================================  
- Coverage    99.21%   99.21%   -0.01%       
===========================================  
  Files          157      157                
  Lines         8422     8421       -1       
===========================================  
- Hits          8356     8355       -1       
  Misses          66       66                
```  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/858?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/detail/move\_chars.hpp](https://app.codecov.io/gh/boostorg/url/pull/858?src=pr&el=tree&filepath=src%2Fdetail%2Fmove_chars.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9tb3ZlX2NoYXJzLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/858?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/858?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [094a535...8789cf4](https://app.codecov.io/gh/boostorg/url/pull/858?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2024-08-15 23:54:19 UTC  
> Url: https://github.com/boostorg/url/pull/858#issuecomment-2292474032  

@sdarwin the "CI / Runner Selection" step got stuck again (already restarted).

---

## Comment 4

> Username: sdarwin  
> Created_at: 2024-08-16 01:35:36 UTC  
> Updated_at: 2024-08-16 01:36:05 UTC  
> Url: https://github.com/boostorg/url/pull/858#issuecomment-2292565776  

@alandefreitas , that means the issue has escalated in some way.   The first time this happened it seemed to be an isolated event.  But now it is repeating, so needs to be investigated more.  
  
There was a job missing runners since yesterday.   That was the problem.  
  
When you restarted the workflow today, it would launch one runner for the selection process. And yesterday's job would steal it.  
  
A work-around would be to view what's happening in the Actions page.   Completely cancel a failing workflow which is missing a few runners.    Then, restart the new workflow from today.   Of course, not ideal.   
  
Perhaps always-on permanent instances could be launched (drone has that), but it increases costs and complexity, if done per-repo.  
  
Something else: ubuntu-latest may still be ubuntu-22.04.  Therefore, to install the newest gcc, specify ubuntu-24.04.   It's safer to not depend on `latest`, but specify the required OS tag.

---

## Comment 5

> Username: sdarwin  
> Created_at: 2024-08-16 15:28:19 UTC  
> Updated_at: 2024-08-16 15:28:41 UTC  
> Url: https://github.com/boostorg/url/pull/858#issuecomment-2293717732  

The philips terraform runners are far from perfect in terms of the documentation, and also design.   I have discovered this variable:  
  
`minimum_running_time_in_minutes: 5 (default) `  
  
If an autoscaled instance fails to complete launch within 5 minutes, it is scaled down.  
  
And yet, that was happening, and you'd even expect it, because of the extensive amount of the software involved in launching an ec2 instance. It needs to upgrade the github runner package, etc.  
  
Increased number to 15 minutes.    
  
Theoretically the bug is fixed now.

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2024-08-16 16:18:03 UTC  
> Url: https://github.com/boostorg/url/pull/858#issuecomment-2293791117  

@sdarwin OK. I'll keep restarting until it works.

---

## Comment 7

> Username: sdarwin  
> Created_at: 2024-08-16 16:52:57 UTC  
> Url: https://github.com/boostorg/url/pull/858#issuecomment-2293840346  

> @sdarwin OK. I'll keep restarting until it works.  
  
Again, "theoretically the bug is fixed now."    The recent problem was caused by losing runners due to a 5 minute timeout.   By setting this to 15 minutes, the runners should not be lost.     DM me in Slack if the same issue continues.

---
