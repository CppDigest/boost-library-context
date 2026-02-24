# #13 Fix -Wdeprecated-copy warning with gcc 9. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2019-04-17 21:16:03 UTC  
> Updated at: 2020-01-23 14:36:38 UTC  
> Merged at: 2020-01-23 14:36:38 UTC  
> Closed at: 2020-01-23 14:36:38 UTC  
> Url: https://github.com/boostorg/tuple/pull/13  

Hi,  
  
This fixes a -Wdeprecated-copy warning with gcc 9, because copy assignment is explicit, but copy constructor is not.  
  
Cheers,  
Romain

---
