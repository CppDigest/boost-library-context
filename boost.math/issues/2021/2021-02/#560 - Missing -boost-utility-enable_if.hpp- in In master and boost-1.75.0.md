# #560 - Missing "boost/utility/enable_if.hpp" in In master and boost-1.75.0 [Closed]

> Username: achoum  
> Created at: 2021-02-26 10:05:06 UTC  
> Updated at: 2021-02-26 14:32:06 UTC  
> Closed at: 2021-02-26 11:38:15 UTC  
> Url: https://github.com/boostorg/math/issues/560  

Hi,  
  
In boost-1.75.0, ``boost/math/special_functions/math_fwd.hpp`` includes ``boost/utility/enable_if.hpp`` (line 31).  
However, ``boost/utility/enable_if.hpp`` does not exist in the boost-1.75.0 version of ``utility``.

---

## Comment 1

> Username: achoum  
> Created at: 2021-02-26 10:36:14 UTC  
> Url: https://github.com/boostorg/math/issues/560#issuecomment-786563039  

Note: This was solved in ``develop``: https://github.com/boostorg/math/commit/ecc70fe65f5e8189c9f7e38e06d7f05d18a48498

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-26 11:38:15 UTC  
> Url: https://github.com/boostorg/math/issues/560#issuecomment-786594806  

Yes it does, it's here: https://github.com/boostorg/core/tree/develop/include/boost/utility

---

## Comment 3

> Username: achoum  
> Created at: 2021-02-26 14:32:06 UTC  
> Url: https://github.com/boostorg/math/issues/560#issuecomment-786682253  

My bad. I assumed this would be in the ``utility`` repository (instead of the ``core`` one).
