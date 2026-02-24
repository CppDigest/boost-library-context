# #360 - conversion to nearest float [Closed]

> Username: afabri  
> Created at: 2021-08-24 20:58:49 UTC  
> Updated at: 2021-12-10 10:03:40 UTC  
> Closed at: 2021-12-09 11:41:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/360  

While the [documentation ](https://www.boost.org/doc/libs/1_77_0/libs/multiprecision/doc/html/boost_multiprecision/tut/conversions.html)states that exact rationals are rounded to nearest when converted to a floating point number, we observed with clang on Apple that this is only true if the rounding mode is set to nearest, as can be seen on [gist.github.com](https://gist.github.com/danston/91f74dfe3b9f6fbf3ba9c3d8920d8343).    This should maybe be made explicit in the manual, or the rounding code should be set as needed internally.  
We didn't investigate yet how that is for VC++, and we only observed it for `cpp_int`.

---

## Comment 1

> Username: afabri  
> Created at: 2021-08-25 13:09:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/360#issuecomment-905486982  

When I change the rounding mode for VC++  with  `  unsigned int dummy; _controlfp_s(&dummy, _RC_UP, _MCW_RC);`  I do not encounter the problem, but maybe this only does not happen for this particular number.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-08-26 17:53:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/360#issuecomment-906618742  

I'll try and look at this in detail next week.

---

## Comment 3

> Username: afabri  
> Created at: 2021-08-27 10:44:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/360#issuecomment-907110163  

And we observed the same problem with gcc and clang on Linux.

---

## Comment 4

> Username: johnmcfarlane  
> Created at: 2021-12-10 10:03:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/360#issuecomment-990802345  

Under what circumstances is it desirable to change the rounding mode? It's this a common thing to do?
