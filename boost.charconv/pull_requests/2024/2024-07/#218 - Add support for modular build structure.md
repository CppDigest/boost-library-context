# #218 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:35:35 UTC  
> Updated at: 2024-08-19 18:30:26 UTC  
> Merged at: 2024-08-19 18:29:45 UTC  
> Closed at: 2024-08-19 18:29:45 UTC  
> Url: https://github.com/boostorg/charconv/pull/218  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-21 04:00:08 UTC  
> Updated_at: 2024-08-19 18:30:26 UTC  
> Url: https://github.com/boostorg/charconv/pull/218#issuecomment-2241451287  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/218?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.54%. Comparing base [(`24b61d2`)](https://app.codecov.io/gh/boostorg/charconv/commit/24b61d28a4edf171f9fce82059b829955ad2ec20?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`966fcaf`)](https://app.codecov.io/gh/boostorg/charconv/commit/966fcafaeb20afff8316ea069cd289ddc91f2e98?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/218/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #218   +/-   ##  
========================================  
  Coverage    94.54%   94.54%             
========================================  
  Files           67       67             
  Lines         8490     8490             
========================================  
  Hits          8027     8027             
  Misses         463      463             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/218?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/218?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [24b61d2...966fcaf](https://app.codecov.io/gh/boostorg/charconv/pull/218?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 16:07:49 UTC  
> Url: https://github.com/boostorg/charconv/pull/218#issuecomment-2295312539  

Please review and merge this PR at your earliest convenience.

---

## Comment 3

> Username: mborland  
> Created_at: 2024-08-19 12:08:25 UTC  
> Url: https://github.com/boostorg/charconv/pull/218#issuecomment-2296419466  

Seems to be fine. @sdarwin can you please take a look at the Drone run? The x64 Linux builds all fail to clone for out of disk space.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2024-08-19 12:52:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/218#issuecomment-2296506405  

@mborland that was 23 days ago. Problem fixed. I have just clicked 'restart' on the job.   If you are an admin of the `boostorg/charconv` repository, you should have the permission in Drone also.   "Cancel" or "Restart" are in the top right corner.

---

## Comment 5

> Username: mborland  
> Created_at: 2024-08-19 12:56:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/218#issuecomment-2296514965  

> @mborland that was 23 days ago. Problem fixed. I have just clicked 'restart' on the job. If you are an admin of the `boostorg/charconv` repository, you should have the permission in Drone also. "Cancel" or "Restart" are in the top right corner.  
  
Thanks. Since it just turned to reviewable I didn't check the date. My bad.

---
