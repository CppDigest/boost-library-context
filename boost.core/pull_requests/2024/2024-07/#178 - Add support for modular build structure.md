# #178 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:31:55 UTC  
> Updated at: 2024-08-21 01:55:24 UTC  
> Merged at: 2024-08-21 01:55:23 UTC  
> Closed at: 2024-08-21 01:55:24 UTC  
> Url: https://github.com/boostorg/core/pull/178  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 22:54:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/178#pullrequestreview-2190074073  

📁 test/Jamfile.v2

```diff
 425 | local CPP11 = [ requires cxx11_variadic_templates cxx11_template_aliases cxx11_decltype cxx11_constexpr cxx11_noexcept ] ;
 426 | 
 426 |- local with-serialization = <library>/boost//serialization/<warnings>off $(warnings-as-errors-off) <undefined-sanitizer>norecover:<link>static $(CPP11) ;
```

> Username: Lastique  
> Created_at: 2024-07-20 22:54:47 UTC  
> Url: https://github.com/boostorg/core/pull/178#discussion_r1685547616  

Why `$(warnings-as-errors-off)` is removed?

> Username: grafikrobot  
> Created_at: 2024-07-20 22:58:00 UTC  
> Url: https://github.com/boostorg/core/pull/178#discussion_r1685548116  

Probably a merge error at some point in the past year.


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 16:03:15 UTC  
> Url: https://github.com/boostorg/core/pull/178#issuecomment-2295311132  

Please review and merge this PR at your earliest convenience.

---
