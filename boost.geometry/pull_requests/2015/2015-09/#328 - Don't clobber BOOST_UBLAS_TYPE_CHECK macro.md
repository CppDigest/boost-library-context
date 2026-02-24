# #328 [transform][strategies] Don't clobber BOOST_UBLAS_TYPE_CHECK macro. [Merged]

> Username: jeremy-murphy  
> Created at: 2015-09-27 09:53:30 UTC  
> Updated at: 2015-10-09 00:26:00 UTC  
> Merged at: 2015-10-09 00:26:00 UTC  
> Closed at: 2015-10-09 00:26:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/328  

This macro is also defined in boost/numeric/ublas/detail/config.hpp.  
  
I assume that this is the behaviour you want, rather than checking the previous value?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-10-09 00:25:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/328#issuecomment-146724821  

Ok thanks!

---
