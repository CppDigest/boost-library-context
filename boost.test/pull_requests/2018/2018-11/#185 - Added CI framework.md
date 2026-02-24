# #185 Added CI framework [Closed]

> Username: jeking3  
> Created at: 2018-11-11 22:52:15 UTC  
> Updated at: 2019-03-01 18:30:21 UTC  
> Closed at: 2019-03-01 18:30:21 UTC  
> Url: https://github.com/boostorg/test/pull/185  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.  
  - Fixed example Jamfile to build examples based on C++ features  
  
Please see my fork's PR for build results.  
https://github.com/jeking3/test/pull/1  
  
A number of builds are failing for various reasons - mostly clang due to release optimizer issues in doc_example22 and doc_example23 that needs to be resolved.  
  
As we also discussed by email, the static and UBSAN analyses may need additional work to annotate the intentional issues (this repository is unique, as we're testing a test library).  I can update this PR to mark those as expected failures once we resolve the others.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-12 01:38:38 UTC  
> Url: https://github.com/boostorg/test/pull/185#issuecomment-437726914  

# [Codecov](https://codecov.io/gh/boostorg/test/pull/185?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@2b1e4cd`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/test/pull/185/graphs/tree.svg?width=650&token=CXdgaxTES0&height=150&src=pr)](https://codecov.io/gh/boostorg/test/pull/185?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop     #185   +/-   ##  
==========================================  
  Coverage           ?   46.72%             
==========================================  
  Files              ?       92             
  Lines              ?     5203             
  Branches           ?     2279             
==========================================  
  Hits               ?     2431             
  Misses             ?     1112             
  Partials           ?     1660  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/test/pull/185?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/test/pull/185?src=pr&el=footer). Last update [2b1e4cd...5b244e2](https://codecov.io/gh/boostorg/test/pull/185?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-11-12 17:00:52 UTC  
> Url: https://github.com/boostorg/test/pull/185#issuecomment-437955509  

#177 can be seen in the build failures for clang here.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2019-03-01 12:20:53 UTC  
> Url: https://github.com/boostorg/test/pull/185#issuecomment-468647577  

In branch `topic/PR-185-CI-framework`, see PR #186, build green. I had to disable the static check though.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2019-03-01 18:30:21 UTC  
> Url: https://github.com/boostorg/test/pull/185#issuecomment-468763727  

In master. Thanks!

---
