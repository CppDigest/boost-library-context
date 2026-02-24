# #23 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:23:42 UTC  
> Updated at: 2025-06-26 14:00:20 UTC  
> Merged at: 2025-06-26 14:00:20 UTC  
> Closed at: 2025-06-26 14:00:20 UTC  
> Url: https://github.com/boostorg/functional/pull/23  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:53:09 UTC  
> Url: https://github.com/boostorg/functional/pull/23#issuecomment-2295308508  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-06-25 23:58:22 UTC  
> Url: https://github.com/boostorg/functional/pull/23#issuecomment-3006566203  

Looks like this is failing across the board because SmartPtr no longer supports C++03.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-06-26 12:43:41 UTC  
> Url: https://github.com/boostorg/functional/pull/23#issuecomment-3008363225  

We might as well fix these remaining failures while we're at it. I don't think GCC 4.4. or 4.6 are relevant anymore, and neither is Clang 3.3. I doubt anyone tries to use C++14 with Clang 3.5 and 3.6 as well.  
  
Fixing the clang++-libc++ one is possible but I don't think we have the time for that so let's just drop it, too.

---
