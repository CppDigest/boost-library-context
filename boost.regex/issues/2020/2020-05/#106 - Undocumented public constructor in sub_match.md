# #106 - Undocumented public constructor in sub_match. [Open]

> Username: sayurin  
> Created at: 2020-05-05 23:17:22 UTC  
> Updated at: 2020-05-05 23:17:22 UTC  
> Url: https://github.com/boostorg/regex/issues/106  

boost::sub_match has undocumented public non-explicit constructor.  
https://github.com/boostorg/regex/blob/d961318aa24a349073cbfc7275201482a19cd3f5/include/boost/regex/v4/sub_match.hpp#L51  
  
So sub_match can convert from BidiIterator.  
```  
boost::csub_match m = "abc";  
```  
Is this expected conversion?
