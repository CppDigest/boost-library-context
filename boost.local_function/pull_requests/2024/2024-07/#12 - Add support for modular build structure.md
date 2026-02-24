# #12 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:31:14 UTC  
> Updated at: 2025-06-26 14:08:37 UTC  
> Merged at: 2025-06-26 14:08:37 UTC  
> Closed at: 2025-06-26 14:08:37 UTC  
> Url: https://github.com/boostorg/local_function/pull/12  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:02:25 UTC  
> Url: https://github.com/boostorg/local_function/pull/12#issuecomment-2295310902  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-06-25 23:54:36 UTC  
> Url: https://github.com/boostorg/local_function/pull/12#issuecomment-3006562044  

I don't think the changes from `toolset: gcc-x` to `toolset: gcc; compiler: g++-x` are necessary.

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2025-06-26 12:39:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/local_function/pull/12#pullrequestreview-2961957905  

📁 .github/workflows/ci.yml

```diff
  22 |-             container: ubuntu:18.04
  22 |             os: ubuntu-latest
  23 |+             container: ubuntu:18.04
```

> Username: pdimov  
> Created_at: 2025-06-26 12:39:03 UTC  
> Url: https://github.com/boostorg/local_function/pull/12#discussion_r2168971634  

The `container`, then `os` order is the correct one because it displays better.


---
