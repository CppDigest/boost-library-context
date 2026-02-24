# #131 - C4706 MSVC warning in zlib.hpp [Closed]

> Username: akortunov  
> Created at: 2021-04-21 08:11:36 UTC  
> Updated at: 2022-06-29 04:27:17 UTC  
> Closed at: 2022-06-29 04:27:17 UTC  
> Url: https://github.com/boostorg/iostreams/issues/131  

Happens at least with Boost 1.76 release or lower.  
If you build a C++ project which includes Boost's zlib.hpp with /W4 warnings level, you may get a such warning:  
```  
boost\iostreams\filter\zlib.hpp(392): warning C4706: assignment within conditional expression  
```  
It is caused by this code from the include/boost/iostreams/filter/zlib.hpp:  
```  
return !(eof_ = result == zlib::stream_end);  
```  
There are at least two possible solutions for this issue:  
1. Add 4706 to the list of disabled warnings in this file:  
```  
#ifdef BOOST_MSVC  
# pragma warning(push)  
# pragma warning(disable:4251 4275 4231 4660 4706)  
#endif  
```  
2. Split this expression:  
```  
eof_ = (result == zlib::stream_end);  
return !eof_;  
```

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2022-02-01 20:22:03 UTC  
> Url: https://github.com/boostorg/iostreams/issues/131#issuecomment-1027252015  

Looks like this was fixed, anyone able to close?
