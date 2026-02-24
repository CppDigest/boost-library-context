# #125 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:26:39 UTC  
> Updated at: 2024-08-31 20:09:36 UTC  
> Merged at: 2024-08-31 20:09:36 UTC  
> Closed at: 2024-08-31 20:09:36 UTC  
> Url: https://github.com/boostorg/optional/pull/125  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:56:48 UTC  
> Url: https://github.com/boostorg/optional/pull/125#issuecomment-2295309400  

Please review and merge this PR at your earliest convenience.

---

## Review 2 [Changes requested]

> Username: akrzemi1  
> Created_at: 2024-08-29 17:39:26 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/optional/pull/125#pullrequestreview-2269627496  

I do not understand Bjam or B2, I need to trust you.   
One thing, though. I recently reduced the number of dependencies, and I think it is not reflected in this PR.

📁 build.jam

```diff
  11 |+         <source>/boost/config//boost_config
  12 |+         <source>/boost/core//boost_core
  13 |+         <source>/boost/detail//boost_detail
```

> Username: akrzemi1  
> Created_at: 2024-08-29 17:33:52 UTC  
> Updated_at: 2024-08-29 17:39:26 UTC  
> Url: https://github.com/boostorg/optional/pull/125#discussion_r1736792950  

I recently reduced the number of dependencies for Optional. It is now  
  
    assert  
    config  
    core  
    move  
    static_assert  
    throw_exception  
    type_traits  
  
Does this PR include this?

---

📁 build.jam

```diff
  18 |-         <source>/boost/type_traits//boost_type_traits
  19 |-         <source>/boost/utility//boost_utility
  10 |+         <library>/boost/assert//boost_assert
```

> Username: akrzemi1  
> Created_at: 2024-08-29 17:34:40 UTC  
> Updated_at: 2024-08-29 17:39:26 UTC  
> Url: https://github.com/boostorg/optional/pull/125#discussion_r1736794364  

Too many dependencies. It should be  
  
    assert  
    config  
    core  
    move  
    static_assert  
    throw_exception  
    type_traits

---

📁 build.jam

```diff
   6 | require-b2 5.2 ;
   7 | 
   8 |+ constant boost_dependencies :
```

> Username: akrzemi1  
> Created_at: 2024-08-29 17:37:06 UTC  
> Updated_at: 2024-08-29 17:39:26 UTC  
> Url: https://github.com/boostorg/optional/pull/125#discussion_r1736798986  

Too many dependencies. It should be  
  
    assert  
    config  
    core  
    move  
    static_assert  
    throw_exception  
    type_traits

> Username: grafikrobot  
> Created_at: 2024-08-31 19:11:04 UTC  
> Url: https://github.com/boostorg/optional/pull/125#discussion_r1739907305  

Thank you for pointing out the change in dependencies (and for reducing dependencies!). Updated the PR to reflect it.


---
