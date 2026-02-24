# #425 Make b2 headers non-optional to simplify instructions [Merged]

> Username: ArbitCode  
> Created at: 2020-01-20 20:02:16 UTC  
> Updated at: 2020-03-26 19:42:37 UTC  
> Merged at: 2020-01-20 22:59:04 UTC  
> Closed at: 2020-01-20 22:59:04 UTC  
> Url: https://github.com/boostorg/gil/pull/425  

There is a need to run   
```shell  
./b2 -j8 headers  
```  
 otherwise, some other directory iterators do not create.  
  
<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-01-20 22:58:06 UTC  
> Url: https://github.com/boostorg/gil/pull/425#issuecomment-576454348  

Technically, there shouldn't be any need to run `./b2 headers` if one aims to build and run tests using B2 too and not just CMake because `b2` will automagically take care of deploying required headers, e.g.:  
  
```  
mloskot@bionic:gil$ ../../b2 -j8 cxxstd=11 test/core  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - Boost.Config Feature Check: cxx11_constexpr : yes  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_template_aliases : yes  
    - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes  
    - Boost.Config Feature Check: cxx11_constexpr : yes  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_template_aliases : yes  
    - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes  
    - BOOST_COMP_GNUC >= 4.3.0 : yes  
...found 1276 targets...  
...updating 746 targets...  
mklink-or-dir ../../boost/gil  
mklink-or-dir ../../boost/mp11  
mklink-or-dir ../../boost/iterator  
link.mklink ../../boost/concept_check.hpp  
mklink-or-dir ../../boost/core  
mklink-or-dir ../../boost/test  
link.mklink ../../boost/mp11.hpp  
mklink-or-dir ../../boost/mpl  
mklink-or-dir ../../boost/preprocessor  
mklink-or-dir ../../boost/detail  
mklink-or-dir ../../boost/type_traits  
link.mklink ../../boost/limits.hpp  
mklink-or-dir ../../boost/numeric  
```  
  
But, let's simplify the instructions for users, having less optional steps means less questions a user needs to answer.

---
