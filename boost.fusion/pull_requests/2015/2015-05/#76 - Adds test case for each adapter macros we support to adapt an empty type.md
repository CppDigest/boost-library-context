# #76 Adds test case for each adapter macros we support to adapt an empty type [Merged]

> Username: daminetreg  
> Created at: 2015-05-15 07:18:14 UTC  
> Updated at: 2015-05-15 13:20:25 UTC  
> Merged at: 2015-05-15 13:20:25 UTC  
> Closed at: 2015-05-15 13:20:25 UTC  
> Url: https://github.com/boostorg/fusion/pull/76  

Hi @djowel,  
  
As #75 already fixed the problem of adapting an empty struct, this PR provides testcases for ADAPT_ADT\* and ADAPT_STRUCT\* macros to ensure we can use any of them with empty types.  
  
This closes IMHO the bug entry https://svn.boost.org/trac/boost/ticket/11269.  
  
I have all tests passing under GCC 4.9.2 & Clang (3.5.1) in the following combinations :   
- bjam cxxflags=" -DBOOST_PP_VARIADICS=0 "   
- bjam cxxflags=" -DBOOST_PP_VARIADICS=1 "  
- bjam cxxflags=" -DBOOST_PP_VARIADICS=0 --std=c++11 "  
- bjam cxxflags=" -DBOOST_PP_VARIADICS=1 --std=c++11 "  
  
Cheers & Enjoy cppnow :smile: Would have loved to be at the x3 presentation.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-05-15 13:20:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/76#issuecomment-102395573  

wonderful!

---
