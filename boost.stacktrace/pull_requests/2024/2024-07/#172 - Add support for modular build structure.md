# #172 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:33:13 UTC  
> Updated at: 2024-09-01 14:23:26 UTC  
> Merged at: 2024-09-01 14:23:26 UTC  
> Closed at: 2024-09-01 14:23:26 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/172  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:04:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/172#issuecomment-2295311552  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2024-08-25 15:15:10 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/172#issuecomment-2308891620  

@grafikrobot could you please take another look at this PR. Something is wrong with CI runs. I've failed to quickly diagnose the problem, may be your experience would find the issue quickly?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-09-01 00:08:32 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/172#issuecomment-2323078836  

> @grafikrobot could you please take another look at this PR. Something is wrong with CI runs. I've failed to quickly diagnose the problem, may be your experience would find the issue quickly?  
  
Figured out the problem. I had inadvertently added /boost/stacktrace//boost_stacktrace as a dependency for all the tests. Which made the linker choose the noop version instead of the specific one each test wants. Should be resolved now.

---

## Comment 4

> Username: coveralls  
> Created_at: 2024-09-01 00:34:50 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/172#issuecomment-2323089255  

[![Coverage Status](https://coveralls.io/builds/69540933/badge)](https://coveralls.io/builds/69540933)  
  
coverage: 86.35%. remained the same  
when pulling **7f2966289a11c0d05fe922971b27f3b58a1d6980 on grafikrobot:modular**  
into **cbf0df774dd7baa2af5a7d28662fd58b90ea8c3d on boostorg:develop**.

---
