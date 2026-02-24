# #71 [algorithms][overlay] remove usused include [Merged]

> Username: mkaravel  
> Created at: 2014-06-20 09:34:55 UTC  
> Updated at: 2014-06-26 23:48:51 UTC  
> Merged at: 2014-06-22 13:27:50 UTC  
> Closed at: 2014-06-22 13:27:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/71  

BTW, this include creates an dependence with <iostream>, which should certainly be avoided.  
  
It also happens that the modified file is not used anywhere else but in algortihm tests, so it can very well be moved in there.

---
