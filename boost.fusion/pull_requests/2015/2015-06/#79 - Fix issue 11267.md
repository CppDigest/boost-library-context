# #79 Fix issue 11267 [Merged]

> Username: Flast  
> Created at: 2015-06-08 13:46:26 UTC  
> Updated at: 2015-06-08 14:07:20 UTC  
> Merged at: 2015-06-08 13:57:09 UTC  
> Closed at: 2015-06-08 13:57:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/79  

This PR will fix [#11267](https://svn.boost.org/trac/boost/ticket/11267).  
It is one of regressions in 1.58.0 release: constexpr support.  
  
> Compiler yields compile error within a function witch used in unevaluate  
> context of constexpr function because of CWG 1581 [2].  
> 1. https://llvm.org/bugs/show_bug.cgi?id=23135  
> 2. http://www.open-std.org/jtc1/sc22/wg21/docs/cwg_active.html#1581  
  
Note: Regenerating preprocessed files is required.

---
