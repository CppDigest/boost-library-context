# #84 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:27:40 UTC  
> Updated at: 2024-08-18 22:22:08 UTC  
> Merged at: 2024-08-18 22:22:08 UTC  
> Closed at: 2024-08-18 22:22:08 UTC  
> Url: https://github.com/boostorg/iterator/pull/84  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 23:09:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/84#pullrequestreview-2190075755  

📁 test/Jamfile.v2

```diff
  26 |-     [ run zip_iterator_test_std_tuple.cpp ]
  27 |-     [ run zip_iterator_test_std_pair.cpp ]
  27 |+     [ run zip_iterator_test_fusion.cpp : : : <source>/boost/assign//boost_assign ]
```

> Username: Lastique  
> Created_at: 2024-07-20 23:09:02 UTC  
> Url: https://github.com/boostorg/iterator/pull/84#discussion_r1685549027  

Why are these `<source>` dependencies? Shouldn't they be normal "linking" dependencies?


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 15:58:02 UTC  
> Url: https://github.com/boostorg/iterator/pull/84#issuecomment-2295309738  

Please review and merge this PR at your earliest convenience.

---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2024-08-18 22:07:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/84#pullrequestreview-2244420398  

📁 build.jam

```diff
  28 |+ explicit
  29 |+     [ alias boost_iterator : : : : <library>$(boost_dependencies) ]
  30 |+     [ alias all : boost_iterator test ]
```

> Username: Lastique  
> Created_at: 2024-08-18 22:07:22 UTC  
> Url: https://github.com/boostorg/iterator/pull/84#discussion_r1721073493  

I'm seeing this `test` target in multiple libraries, but I'm not seeing where it is defined. Is this some common Boost-wide target?

> Username: grafikrobot  
> Created_at: 2024-08-18 22:17:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/84#discussion_r1721074906  

That's referring to the test subdir project. I.e. https://github.com/boostorg/iterator/blob/develop/test/Jamfile.v2 in this lib. Hence the `all` target will also build and run the tests for the library.

> Username: Lastique  
> Created_at: 2024-08-18 22:21:19 UTC  
> Url: https://github.com/boostorg/iterator/pull/84#discussion_r1721075417  

Ah, I see.


---
