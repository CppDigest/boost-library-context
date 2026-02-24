# #194 Add modular library level build file. [Merged]

> Username: grafikrobot  
> Created at: 2024-02-04 15:09:15 UTC  
> Updated at: 2025-06-25 23:43:07 UTC  
> Merged at: 2025-06-25 23:43:07 UTC  
> Closed at: 2025-06-25 23:43:07 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/194  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:05:14 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/194#issuecomment-2295311735  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-06-06 23:44:43 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/194#issuecomment-2951232493  

You can fix the clang-7/8 failures by removing 2a from cxxstd (hardly anyone would use clang 7 with -std=c++2a so there's no point wasting time to "properly" fix that config.)  
  
As for msvc-14.1, it's just not present on windows-2019. Probably just remove it and add msvc-14.3 on windows-2022 or 2025 instead.  
  
msvc-14.2 is going to stop working soon too, because windows-2019 is being retired.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2025-06-07 01:34:45 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/194#issuecomment-2951417071  

@pdimov did that.

---
