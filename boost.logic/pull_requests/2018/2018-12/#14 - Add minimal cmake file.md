# #14 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2018-12-23 20:40:47 UTC  
> Updated at: 2019-01-06 16:52:45 UTC  
> Merged at: 2019-01-05 00:08:14 UTC  
> Closed at: 2019-01-05 00:08:14 UTC  
> Url: https://github.com/boostorg/logic/pull/14  

Supports the use of boost logic as part of a  
parent cmake project via add_subdirectory( libs/logic )  
  
Some more information can be found on the ML: https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M%5B1-25%5D

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-12-23 21:41:23 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-449664882  

# [Codecov](https://codecov.io/gh/boostorg/logic/pull/14?src=pr&el=h1) Report  
> Merging [#14](https://codecov.io/gh/boostorg/logic/pull/14?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/logic/commit/fd021e5eb747eaa77c94720ef1ea17e7e73911ae?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/logic/pull/14/graphs/tree.svg?width=650&token=2RCqoPxHln&height=150&src=pr)](https://codecov.io/gh/boostorg/logic/pull/14?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #14   +/-   ##  
========================================  
  Coverage    63.35%   63.35%             
========================================  
  Files            2        2             
  Lines          131      131             
  Branches        69       69             
========================================  
  Hits            83       83             
  Misses           2        2             
  Partials        46       46  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/logic/pull/14?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/logic/pull/14?src=pr&el=footer). Last update [fd021e5...2686f43](https://codecov.io/gh/boostorg/logic/pull/14?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-12-24 12:40:08 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-449729482  

Do we need CI build jobs on this?

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-12-24 15:36:22 UTC  
> Updated_at: 2018-12-24 15:36:57 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-449747354  

I don't know: different maintainers had very different opinions on that in the past: Some didn't care, some wouldn't merge it unless there is a test for the cmake file and some wouldn't merge it when I added a test.  
  
An example of where I did add tests is here: https://github.com/boostorg/integer/pull/18/files. If you want I can add something similar to this PR (after the holidays ;)).

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2019-01-02 21:37:05 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-450992886  

@jeking3: So, how should I proceed?

---

## Comment 5

> Username: jeking3  
> Created_at: 2019-01-05 00:07:54 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451605943  

How are we going to ensure the CMakeLists.txt file list of primary dependencies is always accurate going forward?  I think that part of the build process should be to analyze the primary dependencies (using the tool @pdimov created?) and fail if they don't match the CMakeLists.txt file.

---

## Comment 6

> Username: Mike-Devel  
> Created_at: 2019-01-05 07:24:12 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451634586  

> How are we going to ensure the CMakeLists.txt file list of primary dependencies is always accurate going forward?  
  
That is indeed a good question and I don't have an automated solution for that. If I was a library maintainer, I'd just manually compare the output of boostdep with the cmake file from time to time, as the dependencies should (imho) not change all that often, but for the CMT that is probably not a viable option.   
  
One partial solution is to build a dummy program using cmake as part of the ci process. That will at least ensure that all dependencies are listed either directly or indirectly, but it doesn't prevent unnecessary dependencies remaining in the list and also doesn't detect if a library that is actually a primary dependency only gets pulled in indirectly.  
  
Another possibility would be to let boostdep generate the dependency list directly into a separate file. That file can get included by the main `CMakeLists.txt` file and the ci could then check if there are any changes between the checked-in and the generated version (`boostdep --cmake logic` will already give you almost what you want and can certainly be extended to fit the syntax here).

---

## Comment 7

> Username: jeking3  
> Created_at: 2019-01-05 15:12:39 UTC  
> Updated_at: 2019-01-05 15:14:11 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451663587  

These dependencies change all the time... any manual solution here will not be sustainable long-term.  Even if there were a build-time check that b2 does it would be good enough to just catch it.  b2 already knows what the direct dependencies are because it ensures those projects are built automatically and/or those headers are linked into the headers folder automatically.  
  
* A dependency that no longer exists but is in CMakeLists.txt  
* A new dependency not in CMakeLists.txt  
  
As it is, I can almost guarantee it will rot and be incorrect before 1.70.0 releases.  
  
@pdimov any thoughts on how you would consider solving this as a build-time failure (b2 preferred; I suppose it could be a CI job each repo inherits though).

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-01-05 15:48:08 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451666143  

As Mike said, the dependency list can reside in a separate file (I used `cmake/dependencies.cmake` in the past) generated by `boostdep --cmake logic`. This is what it outputs today:  
  
```  
# Generated by boostdep --cmake logic  
  
boost_declare_dependency(boost_config INTERFACE boost::config)  
boost_declare_dependency(boost_core INTERFACE boost::core)  
```  
  
I'll need to change it to use `Boost::config` instead of `boost::config` as we're using the former now.  
  
This file could then be included by `CMakeLists.txt` to replace the current `target_link_libraries` call, if an appropriate definition of `boost_declare_dependency` is defined beforehand.  
  
In a Travis job you would then `diff` the output of `boostdep --cmake logic` with the contents of `cmake/dependencies.cmake` and fail on mismatch, as I do f.ex. here:  
  
https://github.com/boostorg/boostdep/blob/develop/.travis.yml#L132  
  
to check whether another boostdep-generated file is up to date.  
  
I, personally, prefer to keep the dependency list manual, especially when it consists of nothing more than `config` and `core`, but automating it has its benefits.

---

## Comment 9

> Username: jeking3  
> Created_at: 2019-01-05 16:03:12 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451667269  

As long as a build fails either locally or in CI that's sufficient to make sure it doesn't rot.  Thanks.  Once you have the output on boostdep modified to use the correct (capitalized) version let me know.

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2019-01-05 16:25:05 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451668842  

>  b2 already knows what the direct dependencies are   
  
Is that really the case for header only dependencies? Where does it get that information from?

---

## Comment 11

> Username: Mike-Devel  
> Created_at: 2019-01-05 16:32:36 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451669383  

@pdimov: How about instead of changing the spelling, add a new output that just creates a global variable with the dependent library names as a list of strings. The main cmake file can then just loop over that list and create the target names via concatenation.

---

## Comment 12

> Username: pdimov  
> Created_at: 2019-01-05 16:42:45 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451670246  

You'd need three variables, INTERFACE, PUBLIC, PRIVATE.

---

## Comment 13

> Username: pdimov  
> Created_at: 2019-01-05 16:47:42 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451670669  

Although there's indeed no reason to prefix with `boost_` and `boost::`, I can just output the name, once. I probably had some scenarios in mind where these two differ, don't remember now what they were, if any.

---

## Comment 14

> Username: pdimov  
> Created_at: 2019-01-06 16:52:45 UTC  
> Url: https://github.com/boostorg/logic/pull/14#issuecomment-451756399  

I changed `boostdep --cmake` so that it now outputs  
  
```  
# Generated by `boostdep --cmake logic`  
  
boost_declare_dependency(INTERFACE config)  
boost_declare_dependency(INTERFACE core)  
```  
  
The original reason for having both `boost_config` and `boost::config` in the output was that the former was the package name (used with `find_package` and `find_dependency`) and the latter was the target name (used for `target_link_libraries`.) These two differ in the case of f.ex. `boost_test` and `boost::unit_test_framework`. But `boostdep` can't really output the correct info for Boost.Test anyway, so I suppose there's no need to keep pretending that it can.

---
