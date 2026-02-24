# #320 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:35:14 UTC  
> Updated at: 2024-08-19 07:40:08 UTC  
> Merged at: 2024-08-19 07:40:08 UTC  
> Closed at: 2024-08-19 07:40:08 UTC  
> Url: https://github.com/boostorg/mysql/pull/320  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: anarthal  
> Created_at: 2024-07-22 13:36:34 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#issuecomment-2242982659  

Looks like the docs build broke: https://drone.cpp.al/boostorg/mysql/784/39/2

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-07-22 13:40:07 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#issuecomment-2242991181  

> Looks like the docs build broke: https://drone.cpp.al/boostorg/mysql/784/39/2  
  
Indeed. I'll be making some changes very soon (which Peter also brought up as a problem) to deal with that.

---

## Review 3 [Commented]

> Username: anarthal  
> Created_at: 2024-07-22 13:45:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mysql/pull/320#pullrequestreview-2191610784  

📁 test/Jamfile

```diff
  37 |+ using openssl : : <search>$(OPENSSL_ROOT)/lib <include>$(OPENSSL_ROOT)/include ;
  38 |+ alias ssl : : <source>/openssl//ssl/<link>shared ;
  39 |+ alias crypto : : <source>/openssl//crypto/<link>shared ;
```

> Username: anarthal  
> Created_at: 2024-07-22 13:39:46 UTC  
> Updated_at: 2024-07-22 13:45:34 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#discussion_r1686572574  

Why do we need this? There is a `using openssl` before this - we must get rid one of the two.  
Are the aliases used anyhow?

> Username: grafikrobot  
> Created_at: 2024-07-22 13:52:51 UTC  
> Updated_at: 2024-07-22 13:52:52 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#discussion_r1686594153  

Good question.. Seems like this was a merge mistake, on my end, at some point in the last year. Will fix.

---

📁 test/Jamfile

```diff
  76 |         <target-os>windows:<define>_WIN32_WINNT=0x0601
  71 |-         <include>../include
  77 |+         <define>BOOST_ASIO_SEPARATE_COMPILATION
```

> Username: anarthal  
> Created_at: 2024-07-22 13:41:34 UTC  
> Updated_at: 2024-07-22 13:45:34 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#discussion_r1686575539  

AFAIK Boost.MySQL does not need this macro. It's true we use it throughout the tests to speed up compilation, but it's not a requirement (actually I should define it out when building in header only mode).

---

📁 test/Jamfile

```diff
  51 | # Requirements to use across targets
  46 |- local requirements = 
  52 |+ local requirements =
```

> Username: anarthal  
> Created_at: 2024-07-22 13:45:29 UTC  
> Updated_at: 2024-07-22 13:45:35 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#discussion_r1686582187  

This is just a question - am I allowed to use the `boost_mysql` target in root `build.jam` in my tests? If it is, I will refactor the test Jamfiles to use it, instead of current `/boost/mysql/test//boost_mysql`.

> Username: grafikrobot  
> Created_at: 2024-07-22 13:54:33 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#discussion_r1686596845  

Yes, that would be allowed, and preferred, once this PR is merged.


📁 build.jam

```diff
   8 |+ project /boost/mysql
   9 |+     : common-requirements
  10 |+         <library>/boost/asio//boost_asio
```

> Username: anarthal  
> Created_at: 2024-07-22 13:43:20 UTC  
> Updated_at: 2024-07-22 13:45:34 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#discussion_r1686579101  

OpenSSL is a hard dependency - shouldn't it be listed here?


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-07-26 02:59:36 UTC  
> Updated_at: 2024-08-17 15:21:24 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#issuecomment-2251871199  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/320?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.03%. Comparing base [(`00bc812`)](https://app.codecov.io/gh/boostorg/mysql/commit/00bc81260cb66289661190943004b31e714bbc02?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a7604c4`)](https://app.codecov.io/gh/boostorg/mysql/commit/a7604c43c99e902f03a921dd08287ed28debd8d6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/320/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/320?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #320   +/-   ##  
========================================  
  Coverage    99.03%   99.03%             
========================================  
  Files          144      144             
  Lines         7122     7122             
========================================  
  Hits          7053     7053             
  Misses          69       69             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/320?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/320?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [00bc812...a7604c4](https://app.codecov.io/gh/boostorg/mysql/pull/320?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 5 [Commented]

> Username: anarthal  
> Created_at: 2024-07-26 09:49:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mysql/pull/320#pullrequestreview-2201393539  

📁 build.jam

```diff
  29 |+ explicit
  30 |+     [ alias boost_mysql : : : : <library>$(boost_dependencies) ]
  31 |+     [ alias all : boost_mysql example test ]
```

> Username: anarthal  
> Created_at: 2024-07-26 09:49:32 UTC  
> Updated_at: 2024-07-26 09:49:33 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#discussion_r1692797475  

Just a question here - what does this "all" imply? Is it used by the superproject anyhow? Examples require an actual MySQL server and would thus fail on such CIs - they're considered "integration tests" from our perspective. They're currently only run in our CIs, and not in Boost's

> Username: grafikrobot  
> Created_at: 2024-07-26 13:52:51 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#discussion_r1693119405  

The `all` target is for my automation benefit. It lets me generically test the build of all libraries uniformly (even if the actual test/example/whatever fail). I use it, for example, in these.. https://github.com/grafikrobot/boost-b2-modular/actions/runs/10021270924 and https://github.com/grafikrobot/boostorg.boost/actions/runs/10021281336. The target should never be used otherwise.


---

## Comment 6

> Username: grafikrobot  
> Created_at: 2024-08-18 16:07:24 UTC  
> Url: https://github.com/boostorg/mysql/pull/320#issuecomment-2295312356  

Please review and merge this PR at your earliest convenience.

---
