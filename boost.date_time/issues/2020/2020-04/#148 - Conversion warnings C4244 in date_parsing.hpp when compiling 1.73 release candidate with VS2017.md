# #148 - Conversion warnings  C4244 in date_parsing.hpp when compiling 1.73 release candidate with VS2017 [Closed]

> Username: thebrandre  
> Created at: 2020-04-10 08:24:31 UTC  
> Updated at: 2020-07-22 15:29:18 UTC  
> Closed at: 2020-07-22 15:29:17 UTC  
> Url: https://github.com/boostorg/date_time/issues/148  

Visual Studio 2017's compiler generates multiple conversion warnings from int literals to unsigned short in the implementation of this function   
```cpp  
inline unsigned short  
    month_str_to_ushort(std::string const& s)   
```	  
> warning C4244: 'initializing': conversion from '_Ty' to '_Ty2', possible loss of data  
boost_fc31e493\boost\date_time\date_parsing.hpp(99): note: see reference to function template instantiation 'std::pair<const _Kty,_Ty>::pair<const char(&)[4],int,0>(_Other1,_Other2 &&) noexcept(false)' being compiled  
[...]  
  
One might just use explicit cast as in the  `BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX` case to resolve that.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-04-10 10:22:21 UTC  
> Url: https://github.com/boostorg/date_time/issues/148#issuecomment-611974127  

Sorry -- what compile options?

---

## Comment 2

> Username: thebrandre  
> Created at: 2020-04-10 11:01:57 UTC  
> Url: https://github.com/boostorg/date_time/issues/148#issuecomment-611984707  

Compiled with warning level /W4.  
Here's a simplified example on [godbolt](https://godbolt.org/z/HGVfpm). gcc and clang don't complain with -Wall enabled.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-04-10 11:08:40 UTC  
> Url: https://github.com/boostorg/date_time/issues/148#issuecomment-611986472  

yep - ok thanks, I'll work on it.

---

## Comment 4

> Username: JeffGarland  
> Created at: 2020-07-22 15:29:17 UTC  
> Url: https://github.com/boostorg/date_time/issues/148#issuecomment-662521025  

Fixed in boost 1.74
