# #97 [CMake] Better compatibility with boost_install [Closed]

> Username: Mike-Devel  
> Created at: 2020-01-14 19:56:33 UTC  
> Updated at: 2020-01-21 09:49:03 UTC  
> Closed at: 2020-01-21 09:49:02 UTC  
> Url: https://github.com/boostorg/regex/pull/97  

boost_install (from tools/cmake) chokes when include path for install interface is set to a fixed value explicitly, but automagically handles the case if the include dir is just set to 'include' without any regards for build/install interface differences.

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2020-01-21 09:49:02 UTC  
> Url: https://github.com/boostorg/regex/pull/97#issuecomment-576601635  

PR https://github.com/boostorg/regex/pull/99 by Peter dimove supersedes this PR.

---
