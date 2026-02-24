# #35 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-10 11:59:50 UTC  
> Updated at: 2019-04-16 15:42:03 UTC  
> Merged at: 2019-04-16 15:42:03 UTC  
> Closed at: 2019-04-16 15:42:03 UTC  
> Url: https://github.com/boostorg/rational/pull/35  

- CMake file only supports add_subdirectory workflow.  
- Provides Boost::rational target  
- Does not support installation  
- Does not compile/run unit tests

---

## Review 1 [Approved]

> Username: jeking3  
> Created_at: 2019-03-10 12:12:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/rational/pull/35#pullrequestreview-212599666  

Still not wild about the static list of dependencies, without something in the build process to fail if they change. :)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-03-10 12:38:08 UTC  
> Updated_at: 2019-04-14 14:28:41 UTC  
> Url: https://github.com/boostorg/rational/pull/35#issuecomment-471284255  

# [Codecov](https://codecov.io/gh/boostorg/rational/pull/35?src=pr&el=h1) Report  
> Merging [#35](https://codecov.io/gh/boostorg/rational/pull/35?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/rational/commit/3fc4192167fa289035878cc4f007208ff6316e1e?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/rational/pull/35/graphs/tree.svg?width=650&token=GWRPyIttVH&height=150&src=pr)](https://codecov.io/gh/boostorg/rational/pull/35?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #35   +/-   ##  
========================================  
  Coverage    67.52%   67.52%             
========================================  
  Files            1        1             
  Lines          271      271             
  Branches        90       90             
========================================  
  Hits           183      183             
  Misses           3        3             
  Partials        85       85  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/rational/pull/35?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/rational/pull/35?src=pr&el=footer). Last update [3fc4192...3f1932b](https://codecov.io/gh/boostorg/rational/pull/35?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2019-03-10 15:21:18 UTC  
> Url: https://github.com/boostorg/rational/pull/35#issuecomment-471315502  

If I understood how your travis file worked, I could probably cobble something together.   
  
I wrote up a basic test procedure for a non-header only library here: https://github.com/boostorg/filesystem/pull/106  
  
in principle, the same is applicable to Boost.Rational, except that it is not the compilation of boost_rational itself that would test that all necessary dependencies are specified, but some excutable, that includes boost/rational.hpp.   
  
I can e.g. easily add a cmake file that compiles and runs the unit-tests if you are able to extend the travis file according to the steps above. It is not quite the same as checking the list of dependencies against the output of boostdep (e.g. it doesn't cover the case of specifying unneded dependencies or dependencies that have been forgotten, but included transitively from another library), but it should be close enough to prevent any unnoticed breakage.  
  
The same can be don for mpl (in fact, that is the way I tested the PR myself). You think you could do this?

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2019-03-31 14:28:49 UTC  
> Url: https://github.com/boostorg/rational/pull/35#issuecomment-478346819  

@jeking3: I added the unit-test file. Currrently only one test can be run using cmake, because Boost.Build doesn't have a cmake file yet, but it is enough to ensure that all dependencies are available.   
Any comments?

---
