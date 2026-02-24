# #25 Fixed missing noreturn [Merged]

> Username: Kojoley  
> Created at: 2016-09-01 16:57:48 UTC  
> Updated at: 2016-09-04 16:16:45 UTC  
> Merged at: 2016-09-04 16:16:45 UTC  
> Closed at: 2016-09-04 16:16:45 UTC  
> Url: https://github.com/boostorg/regex/pull/25  

Fixes following warning:  
  
```  
../../../boost/regex/pending/unicode_iterator.hpp:121:1: warning: function 'invalid_utf32_code_point' could be declared with attribute 'noreturn' [-Wmissing-noreturn]  
{  
^  
```

---
