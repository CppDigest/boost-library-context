# #19 Fix test compilation [Merged]

> Username: MarcelRaad  
> Created at: 2015-09-18 08:16:10 UTC  
> Updated at: 2017-09-19 07:55:24 UTC  
> Merged at: 2015-09-23 01:03:05 UTC  
> Closed at: 2015-09-23 01:03:05 UTC  
> Url: https://github.com/boostorg/iterator/pull/19  

boost/iterator.hpp was implicitly dragged in via boost/operators.hpp, from which it was removed in https://github.com/boostorg/utility/commit/cb6500161b682a66438604e75ab901c46e42430d. It's not needed anyway, all it does is map boost::iterator to std::iterator.

---
