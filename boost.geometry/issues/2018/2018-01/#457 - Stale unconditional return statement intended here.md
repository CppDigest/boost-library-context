# #457 - Stale unconditional return statement intended here? [Closed]

> Username: DanielaE  
> Created at: 2018-01-25 17:17:52 UTC  
> Updated at: 2018-01-25 20:09:57 UTC  
> Closed at: 2018-01-25 20:09:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/457  

In `isea.hpp` line 1017 you will see this:  
```  
   ...  
   hex->x = ((int)v.x << 4) + quad;  
   hex->y = v.y;  
  
   return 1;     <-------------  
  
   d = (int)v.x;  
   i = (int)v.y;  
   ...  
```  
Because of this unconditional `return` statement amidst function `isea_hex`, msvc barfs bursts of compiler warnings on each encounter:  
```  
boost\geometry\srs\projections\proj\isea.hpp(1019) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1023) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1024) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1030) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1032) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1033) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1035) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1036) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1039) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1048) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1050) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1052) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1053) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1056) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1057) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1058) : warning C4702: unreachable code  
boost\geometry\srs\projections\proj\isea.hpp(1061) : warning C4702: unreachable code  
```  
Is this `return` statement really intended here?

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-01-25 19:07:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/457#issuecomment-360567574  

This file was automatically converted from proj4. And it seems that the converter didn't care about preprocessor directives. See: https://github.com/OSGeo/proj.4/blob/master/src/PJ_isea.c#L927  
So AFAIU the code below could be commented out or like in proj4 disabled with `#ifdef`. I'll fix it, thanks.

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-01-25 20:09:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/457#issuecomment-360585623  

Fixed.
