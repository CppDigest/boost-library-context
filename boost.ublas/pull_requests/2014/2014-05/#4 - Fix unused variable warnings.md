# #4 Fix unused variable warnings [Merged]

> Username: awulkiew  
> Created at: 2014-05-17 23:40:29 UTC  
> Updated at: 2014-07-12 05:52:46 UTC  
> Merged at: 2014-06-09 19:36:45 UTC  
> Closed at: 2014-06-09 19:36:45 UTC  
> Url: https://github.com/boostorg/ublas/pull/4  

There is also an unused variable in: boost/numeric/ublas/functional.hpp:1138: unused parameter 'j'  
but in this case it can't be commented out. boost::ignore_unused_variable_warning() defined in boost/concept_check.hpp could be used or similar function added to Ublas.

---
