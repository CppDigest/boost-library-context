# #114 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:23:10 UTC  
> Updated at: 2024-08-18 21:55:03 UTC  
> Merged at: 2024-08-18 21:55:03 UTC  
> Closed at: 2024-08-18 21:55:03 UTC  
> Url: https://github.com/boostorg/parameter/pull/114  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 23:44:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/parameter/pull/114#pullrequestreview-2190080250  

📁 test/Jamfile.v2

```diff
 305 |             <define>BOOST_PARAMETER_MAX_ARITY=5
 306 |             <define>BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY=6
 307 |+             <source>/boost/graph//boost_graph
```

> Username: Lastique  
> Created_at: 2024-07-20 23:44:49 UTC  
> Url: https://github.com/boostorg/parameter/pull/114#discussion_r1685552529  

Why are these `<source>` dependencies?


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 15:52:31 UTC  
> Url: https://github.com/boostorg/parameter/pull/114#issuecomment-2295308338  

Please review and merge this PR at your earliest convenience.

---
