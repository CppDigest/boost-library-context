# #13 [Trac #11502] Fix "always true comparison" warning in narrow_encoding.hpp [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2015-09-02 17:03:35 UTC  
> Updated at: 2016-02-10 10:36:39 UTC  
> Merged at: 2016-02-10 10:36:38 UTC  
> Closed at: 2016-02-10 10:36:39 UTC  
> Url: https://github.com/boostorg/property_tree/pull/13  

Hi,  
  
This is a small fix for Trac #11502 https://svn.boost.org/trac/boost/ticket/11502  
  
gcc 4.9 reports us this warning:  
error: comparison is always true due to limited range of data type [-Werror=type-limits]   
  
Cheers,  
Romain

---
