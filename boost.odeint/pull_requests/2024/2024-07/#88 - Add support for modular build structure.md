# #88 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:30:43 UTC  
> Updated at: 2024-08-19 14:28:32 UTC  
> Merged at: 2024-08-19 14:27:35 UTC  
> Closed at: 2024-08-19 14:27:35 UTC  
> Url: https://github.com/boostorg/odeint/pull/88  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-27 20:11:27 UTC  
> Updated_at: 2024-08-19 14:28:32 UTC  
> Url: https://github.com/boostorg/odeint/pull/88#issuecomment-2254243255  

## [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/88?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.37%. Comparing base [(`48ff7b9`)](https://app.codecov.io/gh/boostorg/odeint/commit/48ff7b90330ac4d0de90524a6cdd90e66a2cb2f2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`44589d8`)](https://app.codecov.io/gh/boostorg/odeint/commit/44589d825ad9a5dc41246bd8ea588234c4367ef7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/odeint/pull/88/graphs/tree.svg?width=650&height=150&src=pr&token=Er6mj5aLOG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/odeint/pull/88?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #88   +/-   ##  
========================================  
  Coverage    99.37%   99.37%             
========================================  
  Files          168      168             
  Lines         6702     6702             
========================================  
  Hits          6660     6660             
  Misses          42       42             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/odeint/pull/88?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/88?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [48ff7b9...44589d8](https://app.codecov.io/gh/boostorg/odeint/pull/88?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 16:01:48 UTC  
> Url: https://github.com/boostorg/odeint/pull/88#issuecomment-2295310736  

Please review and merge this PR at your earliest convenience.

---

## Review 3 [Commented]

> Username: mborland  
> Created_at: 2024-08-19 12:50:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/odeint/pull/88#pullrequestreview-2245464144  

Is this now `boost_odeint` instead of `boost_numeric_odeint`? In build.jam that alias seems to point to the former.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2024-08-19 14:02:44 UTC  
> Url: https://github.com/boostorg/odeint/pull/88#issuecomment-2296659427  

> Is this now `boost_odeint` instead of `boost_numeric_odeint`? In build.jam that alias seems to point to the former.  
  
Yes. I normalized all the B2 global project names to `/boost/<repo-name>`. As it allows for consistent search of the projects once this is used in package managers (or manually) that will key on the repo name instead of the location in the super-project.

---
