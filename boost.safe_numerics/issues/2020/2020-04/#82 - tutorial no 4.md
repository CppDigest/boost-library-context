# #82 - tutorial no 4 [Closed]

> Username: GregKon  
> Created at: 2020-04-06 12:38:04 UTC  
> Updated at: 2020-05-28 14:09:55 UTC  
> Closed at: 2020-05-28 14:09:55 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/82  

Dear Robert  
  
Mixing signed and unsigned in comparison: https://www.boost.org/doc/libs/1_72_0/libs/safe_numerics/doc/html/tutorial/4.html  
  
Example will works for types at least int wide (int + long long int).   
  
But not for int8_t/uint8_t and int16_t/uint16_t, however with types smaller width then int, comparison will produce correct result (due safe promotion, there is also not compiler warning)  
  
However, my question is:  
1. Does it is intention of safe_numeric to do not detect mixing signed/unsigned in comparison if may produce correct results?  
  
I just try to understand the idea of safe_numeric, from other hand user may expect exception. Well at least me was a little bit surprised since my standards do not allow on such signed/unsigned comparison on any types (for simplicity).  
  
/Greg

---

## Comment 1

> Username: robertramey  
> Created at: 2020-04-06 13:24:17 UTC  
> Updated at: 2020-04-06 13:24:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/82#issuecomment-609792138  

The intention of the safe numerics library can simply stated:  
  
Permit no invalid arithmetic result to go undetected.  
  
It's under appreciated how important it is to be able to state the purpose of this library in this way.  In fact, it's the same goes all libraries.  Most lose their way by trying to do multiple things at once and end up being a bag of miscellaneous features rather than the embodiment of a clear (though perhaps abstract) idea one can hold in his brain.  
  
Note that the first thought which occurred to me was:  
  
Guarantee that all numeric operations produce valid results.   
  
But one soon discovers that this is impossible.  At this point it's tempting to give up and just try to rely on testing.  But stepping back and re-thinking the problem, leads to the above - which brings to the point were we are know.  I can prove that my program will never fail without letting me know somehow.

---

## Comment 2

> Username: GregKon  
> Created at: 2020-04-06 13:45:36 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/82#issuecomment-609804219  

it's clear. thank you.
