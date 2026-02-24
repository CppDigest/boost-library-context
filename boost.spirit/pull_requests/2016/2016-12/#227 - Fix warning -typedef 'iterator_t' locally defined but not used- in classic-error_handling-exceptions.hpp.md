# #227 Fix warning "typedef 'iterator_t' locally defined but not used" in classic/error_handling/exceptions.hpp [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2016-12-06 10:39:00 UTC  
> Updated at: 2016-12-06 14:02:59 UTC  
> Merged at: 2016-12-06 14:02:59 UTC  
> Closed at: 2016-12-06 14:02:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/227  

Hi,  
  
Here is a small warning fix in classic/error_handling/exceptions.hpp when using gcc 4.9 and -Wall -Wextra:  
  
boost/spirit/home/classic/error_handling/exceptions.hpp:143:51: warning: typedef 'iterator_t' locally defined but not used [-Wunused-local-typedefs]  
             typedef typename ScannerT::iterator_t iterator_t;  
  
Ok for the trunk ?  
  
Cheers,  
Romain

---
