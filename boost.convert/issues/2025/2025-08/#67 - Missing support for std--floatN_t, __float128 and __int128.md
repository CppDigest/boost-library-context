# #67 - Missing support for std::floatN_t, __float128 and __int128 [Open]

> Username: gpeterhoff  
> Created at: 2025-08-09 22:06:11 UTC  
> Updated at: 2025-08-09 22:24:07 UTC  
> Url: https://github.com/boostorg/convert/issues/67  



---

## Comment 1

> Username: yet-another-user  
> Created at: 2025-08-09 22:24:07 UTC  
> Url: https://github.com/boostorg/convert/issues/67#issuecomment-3172140472  

Huh, I was not even aware of these weird types in C++23. :-) I suspect you'll have to elaborate what exactly you'd like to see. There are a few converters and some of  them are wrappers helping to incorporate, say, lexical_cast, printf, std::stream into the framework. So, float128 support (or the lack of such) depends on the actual converters. The most efficient, quickest solution might be you implementing, adding whatever functionality you need to whatever converter you use and then submit your code for a merge.
