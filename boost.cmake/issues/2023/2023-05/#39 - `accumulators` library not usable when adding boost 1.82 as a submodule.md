# #39 - `accumulators` library not usable when adding boost 1.82 as a submodule [Closed]

> Username: greg7mdp  
> Created at: 2023-05-07 13:57:20 UTC  
> Updated at: 2023-05-08 13:37:41 UTC  
> Closed at: 2023-05-07 14:28:54 UTC  
> Url: https://github.com/boostorg/cmake/issues/39  

When using boost 1.82 as a submodule (`add_subdirectory(boost)`), I can use the boost libraries successfully, except for `accumulators`.  
  
When I try adding the dependency to `Boost::accumulators`, I have:  
```  
CMake Error at libraries/boost/libs/accumulators/CMakeLists.txt:15 (target_link_libraries):  
  The link interface of target "boost_accumulators" contains:  
  
    Boost::numeric_ublas  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-05-07 14:19:02 UTC  
> Url: https://github.com/boostorg/cmake/issues/39#issuecomment-1537453253  

The reason for this is that Boost.uBLAS is still missing CMake support. I tried to add it before, and the maintainers reverted my change. Then I submitted a PR (https://github.com/boostorg/ublas/pull/142), but they haven't merged it yet. You could comment on the PR and request it to be merged - let's hope they listen. :-)

---

## Comment 2

> Username: greg7mdp  
> Created at: 2023-05-07 14:28:54 UTC  
> Url: https://github.com/boostorg/cmake/issues/39#issuecomment-1537455272  

Thank you @pdimov , I did add a comment requesting the merge of this PR.

---

## Comment 3

> Username: greg7mdp  
> Created at: 2023-05-08 13:37:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/39#issuecomment-1538376412  

Meanwhile I can solve my issue by adding these lines in my CMakeLists.txt to provide the `Boost::numeric_ublas` target.  
  
```  
add_library(boost_numeric_ublas INTERFACE)  
add_library(Boost::numeric_ublas ALIAS boost_numeric_ublas)  
```
