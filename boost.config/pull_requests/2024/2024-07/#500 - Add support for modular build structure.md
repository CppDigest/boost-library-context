# #500 Add support for modular build structure. [Closed]

> Username: grafikrobot  
> Created at: 2024-07-20 22:23:47 UTC  
> Updated at: 2025-04-15 09:40:57 UTC  
> Closed at: 2025-04-15 09:40:57 UTC  
> Url: https://github.com/boostorg/config/pull/500  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:53:15 UTC  
> Url: https://github.com/boostorg/config/pull/500#issuecomment-2295308534  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-04-14 15:12:36 UTC  
> Url: https://github.com/boostorg/config/pull/500#issuecomment-2802046631  

Hi @grafikrobot and thanks for this, two quick questions:  
  
1) Why the extra dependencies libs/static_assert libs/throw_exception libs/preprocessor when we really shouldn't have any pretty much.  
  
2) I see project requirements clause now has:  
  
```  
project  
    : requirements  
      <toolset>gcc:<cxxflags>-Wno-deprecated-declarations  
      <library>/boost/core//boost_core  
      <library>/boost/detail//boost_detail  
      <library>/boost/type_traits//boost_type_traits  
;  
```  
  
Again why the need?  
  
I note that a similar clause in Boost.Math just recently completely broken our CI, so I removed it: https://github.com/boostorg/math/pull/1254.  I wondered where that had come from ;)  Was this wrong??  The problem is it's a heavyweight dependency that makes every target in the Jamfile depend on the properties of those other libraries, rather than just the targets that actually need it.  
  
Thanks!

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2025-04-14 15:52:49 UTC  
> Url: https://github.com/boostorg/config/pull/500#issuecomment-2802164505  

> 1. Why the extra dependencies libs/static_assert libs/throw_exception libs/preprocessor when we really shouldn't have any pretty much.  
  
Because Boost.Core needs them. And the CI runs fail if they don't have those.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-04-15 09:40:57 UTC  
> Url: https://github.com/boostorg/config/pull/500#issuecomment-2804455120  

Merged from parallel PR.

---
