# #5 Fix compilation with gcc 4.5 in C++11 mode [Merged]

> Username: Lastique  
> Created at: 2014-04-09 20:29:21 UTC  
> Updated at: 2014-05-10 04:59:13 UTC  
> Merged at: 2014-05-10 04:59:10 UTC  
> Closed at: 2014-05-10 04:59:10 UTC  
> Url: https://github.com/boostorg/utility/pull/5  

Gcc 4.5 does not allow non-public defaulted functions, so fall back to the C++03 version. Also replaced the deprecated macros with the new ones and adjusted formatting.  
  
Originated from: http://thread.gmane.org/gmane.comp.lib.boost.devel/250206

---
