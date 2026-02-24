# #38 Fix compilation [Merged]

> Username: MarcelRaad  
> Created at: 2015-09-18 07:54:30 UTC  
> Updated at: 2015-09-18 08:28:12 UTC  
> Merged at: 2015-09-18 08:08:01 UTC  
> Closed at: 2015-09-18 08:08:01 UTC  
> Url: https://github.com/boostorg/range/pull/38  

boost::detail::iterator_traits was used without including the necessary header file. It's deprecated anyway and only maps to std::iterator_traits.

---
