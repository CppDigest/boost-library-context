# #114 Add BOOST_NO_CXX11_SFINAE_EXPR defect macro [Merged]

> Username: glenfe  
> Created at: 2017-02-04 06:29:36 UTC  
> Updated at: 2017-02-06 05:59:46 UTC  
> Merged at: 2017-02-05 19:39:12 UTC  
> Closed at: 2017-02-05 19:39:12 UTC  
> Url: https://github.com/boostorg/config/pull/114  

This proposes a new BOOST_NO_CXX11_SFINAE_EXPR defect macro whose absence will indicate support for C++11 SFINAE expressions. The test case shows C++11 code which will not compile with MSVC (any version) and g++4.7 (with -std=c++11) even when the existing BOOST_NO_SFINAE_EXPR macro is not defined.

---

## Comment 1

> Username: glenfe  
> Created_at: 2017-02-04 18:25:01 UTC  
> Url: https://github.com/boostorg/config/pull/114#issuecomment-277464775  

John, let me know if you need more context (hopefully the test case is illustrative enough) or if I need to change any other files (I just updated the pull request with the Quick book documentation entry).

---
