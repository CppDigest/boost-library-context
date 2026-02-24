# #11 Fix output of NaN on msvc-14. [Merged]

> Username: mkurdej  
> Created at: 2014-09-16 10:17:40 UTC  
> Updated at: 2014-09-16 14:11:30 UTC  
> Merged at: 2014-09-16 14:08:53 UTC  
> Closed at: 2014-09-16 14:08:53 UTC  
> Url: https://github.com/boostorg/units/pull/11  

Previous versions outputted `1.#IND`, now the output is `nan(ind)` for an indefinite NaN (conformance to C99).  
  
See: http://blogs.msdn.com/b/vcblog/archive/2014/06/18/crt-features-fixes-and-breaking-changes-in-visual-studio-14-ctp1.aspx

---
