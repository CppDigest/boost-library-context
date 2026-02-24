# #26 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:25:42 UTC  
> Updated at: 2025-06-26 12:40:41 UTC  
> Merged at: 2025-06-26 12:40:41 UTC  
> Closed at: 2025-06-26 12:40:41 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/26  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:55:39 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/26#issuecomment-2295309126  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-06-25 23:55:14 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/26#issuecomment-3006562736  

I don't think the changes from `toolset: gcc-x` to `toolset: gcc; compiler: g++-x` are necessary. Also, I'm not sure about the `cxxstd` ones.

---
