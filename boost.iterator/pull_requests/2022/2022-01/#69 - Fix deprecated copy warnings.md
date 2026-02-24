# #69 Fix deprecated copy warnings [Closed]

> Username: apolukhin  
> Created at: 2022-01-13 10:52:37 UTC  
> Updated at: 2022-01-14 10:26:31 UTC  
> Closed at: 2022-01-13 14:00:29 UTC  
> Url: https://github.com/boostorg/iterator/pull/69  

Implicit special member functions of counting_iterator made explicit in this PR.  
  
Along with https://github.com/boostorg/concept_check/pull/35 ut fixes the compilation and test runs with `./b2 libs/iterator/test/ cxxstd=2a,03,11,17,14 "cxxflags=-Werror=deprecated-copy" toolset=clang-13 -j4`

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-01-13 14:00:37 UTC  
> Url: https://github.com/boostorg/iterator/pull/69#issuecomment-1012162895  

Thanks.

---
