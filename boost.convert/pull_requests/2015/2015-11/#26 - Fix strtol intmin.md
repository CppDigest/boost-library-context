# #26 Fix strtol intmin [Merged]

> Username: res2k  
> Created at: 2015-11-22 11:34:38 UTC  
> Updated at: 2015-11-23 07:20:18 UTC  
> Merged at: 2015-11-22 21:35:13 UTC  
> Closed at: 2015-11-22 21:35:13 UTC  
> Url: https://github.com/boostorg/convert/pull/26  

This fixes:  
- A blooper where the strtol 'int_to_str' test didn't actually test int.  
- strtol itself: minimum integer values were not handled correctly. (Which was obscured by above blooper).

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2015-11-22 22:11:15 UTC  
> Url: https://github.com/boostorg/convert/pull/26#issuecomment-158807856  

Re: "blooper"... I missed it too. :-) Nice catch.  
Re: "unsigned" in i_to_str()... the additional min tests that you added convinced me :-) that unsigned was actually needed. Otherwise, negative min was not handled correctly (could not be negated). Many thanks.  
  
"typedef  ... unsigned_type" in i_to_str() was missing "typename"... Did it compile for you? My GCC (correctly) requires "typedef  typename ... unsigned_type"... Or is it C++11 feature?

---

## Comment 2

> Username: res2k  
> Created_at: 2015-11-22 22:32:46 UTC  
> Updated_at: 2015-11-23 07:20:18 UTC  
> Url: https://github.com/boostorg/convert/pull/26#issuecomment-158811512  

I built for me, but I used MSVC 14... AFAIK it's more lenient about typename than gcc.

---
