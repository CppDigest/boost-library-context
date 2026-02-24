# #195 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:28:22 UTC  
> Updated at: 2025-04-15 11:37:52 UTC  
> Merged at: 2024-08-19 08:22:11 UTC  
> Closed at: 2024-08-19 08:22:12 UTC  
> Url: https://github.com/boostorg/type_traits/pull/195  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:58:52 UTC  
> Url: https://github.com/boostorg/type_traits/pull/195#issuecomment-2295309931  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-04-15 11:37:50 UTC  
> Url: https://github.com/boostorg/type_traits/pull/195#issuecomment-2804751305  

Please, merge this to master. Boost.Utility CI depends on this change and is currently failing (specifically, it needs the `/boost/type_traits//testing` target).

---
