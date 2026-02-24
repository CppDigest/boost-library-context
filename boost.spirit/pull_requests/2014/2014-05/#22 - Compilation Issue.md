# #22 Compilation Issue [Merged]

> Username: vtnerd  
> Created at: 2014-05-03 03:45:15 UTC  
> Updated at: 2014-08-18 15:22:57 UTC  
> Merged at: 2014-05-03 03:52:11 UTC  
> Closed at: 2014-05-03 03:52:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/22  

Caught this while re-merging code for the extensions seek parser. Including x3/core.hpp will result in compilation issue because raw_attribute_type is not declared. Including x3/directive.hpp will result in compilation issue because several headers in raw directive were not included.  
  
I don't know if this is the desired way to fix the missing symbol, so nudge me in the proper direction if necessary.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-05-03 03:52:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/22#issuecomment-42095290  

I think this solution is good.

---
