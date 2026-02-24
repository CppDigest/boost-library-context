# #39 Fix test compilation [Merged]

> Username: MarcelRaad  
> Created at: 2015-09-21 22:56:09 UTC  
> Updated at: 2016-01-04 16:37:15 UTC  
> Merged at: 2016-01-04 16:36:42 UTC  
> Closed at: 2016-01-04 16:36:42 UTC  
> Url: https://github.com/boostorg/range/pull/39  

boost::detail::iterator_traits was used without including the necessary header  
file. It's deprecated anyway and only maps to std::iterator_traits. Also,  
utility was missing for std::pair.

---
