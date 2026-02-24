# #114 - Broken link for libstdc++ docs [Closed]

> Username: jwakely  
> Created at: 2021-01-20 17:21:26 UTC  
> Updated at: 2021-02-10 04:47:30 UTC  
> Closed at: 2021-02-10 04:47:30 UTC  
> Url: https://github.com/boostorg/predef/issues/114  

https://github.com/boostorg/predef/blob/9c46a94ae5a08560b1c89603ab8279ac28687e91/include/boost/predef/library/std/stdcpp3.h#L19  
  
This causes a broken URL at  
https://www.boost.org/doc/libs/1_75_0/libs/predef/doc/index.html#_boost_lib_std_gnu  
  
The link is rendered as "[http://gcc.gnu.org/libstdc/\[GNU libstdc](http://gcc.gnu.org/libstdc/[GNU%20libstdc)] Standard C++ library."  
  
You probably want to link to https://gcc.gnu.org/onlinedocs/libstdc++/ because the URL that you use hasn't existed for some time, even if it was rendered correctly.

---

## Comment 1

> Username: jwakely  
> Created at: 2021-01-20 17:25:49 UTC  
> Url: https://github.com/boostorg/predef/issues/114#issuecomment-763807903  

> You probably want to link to https://gcc.gnu.org/onlinedocs/libstdc++/ because the URL that you use hasn't existed for some time, even if it was rendered correctly.  
  
And presumably using `https://gcc.gnu.org/onlinedocs/libstdc%2b%2b/` in the `stdcpp3.h` header would prevent the `+` characters being treated as markup, and is still a valid URL so can be copied and pasted to the browser and elsewhere.

---

## Comment 2

> Username: Rashika101  
> Created at: 2021-01-23 07:22:28 UTC  
> Url: https://github.com/boostorg/predef/issues/114#issuecomment-765882109  

Hey!! are you willing to accept new contributors? I am interested into contributing to this repository so, if you could explain a bit more this feature request, it would be great.  
Thanks
