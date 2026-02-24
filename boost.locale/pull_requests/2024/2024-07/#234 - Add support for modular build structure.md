# #234 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:31:04 UTC  
> Updated at: 2024-08-27 15:41:59 UTC  
> Merged at: 2024-08-27 15:33:09 UTC  
> Closed at: 2024-08-27 15:33:09 UTC  
> Url: https://github.com/boostorg/locale/pull/234  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:02:13 UTC  
> Url: https://github.com/boostorg/locale/pull/234#issuecomment-2295310852  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2024-08-26 15:30:05 UTC  
> Updated_at: 2024-08-26 15:30:15 UTC  
> Url: https://github.com/boostorg/locale/pull/234#issuecomment-2310490678  

@grafikrobot I rebased your branch to remove the sync commits without any changes.  
  
I then fixed the dynamic build as `<link>shared:<define>BOOST_LOCALE_DYN_LINK=1` was missing from the usage-requirements, see https://github.com/boostorg/locale/pull/234/commits/3ff8e5a3f029b125f5d286957f979fc71f264cde  
  
Can you verify the current state please?  
  
Is it possible to have keyword arguments for `lib`  in B2 and add the `common-requirements` there too which currently only exist for `project`? That would reduce the duplication and avoid the above issue. It also removes the current practice of commenting what the arguments to `lib` mean.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-08-27 15:35:46 UTC  
> Updated_at: 2024-08-27 15:41:59 UTC  
> Url: https://github.com/boostorg/locale/pull/234#issuecomment-2312898919  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/234?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.76%. Comparing base [(`84d91b3`)](https://app.codecov.io/gh/boostorg/locale/commit/84d91b30263e07c87bca8d19f8f26fe81a882d73?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3ff8e5a`)](https://app.codecov.io/gh/boostorg/locale/commit/3ff8e5a3f029b125f5d286957f979fc71f264cde?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 11 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/234/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/234?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #234   +/-   ##  
========================================  
  Coverage    95.76%   95.76%             
========================================  
  Files          116      116             
  Lines        10035    10035             
========================================  
  Hits          9610     9610             
  Misses         425      425             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/234?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/234?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [84d91b3...3ff8e5a](https://app.codecov.io/gh/boostorg/locale/pull/234?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
