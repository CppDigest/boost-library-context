# #6 Fix unused variable warnings [Merged]

> Username: awulkiew  
> Created at: 2014-05-17 23:01:42 UTC  
> Updated at: 2014-07-04 22:46:07 UTC  
> Merged at: 2014-05-19 01:55:39 UTC  
> Closed at: 2014-05-19 01:55:39 UTC  
> Url: https://github.com/boostorg/test/pull/6  

The warnings are also generated for:  
boost/test/impl/execution_monitor.ipp:1329 -> enable( unsigned mask )  
boost/test/impl/execution_monitor.ipp:1364 -> disable( unsigned mask )  
but in this case the variable can't be commented out. boost::ignore_unused_variable_warning() defined in boost/concept_check.hpp could be used (I don't know it Test should depend on it), this function could be separated from ConceptCheck and put somewhere else first or similar function could be defined inside the Test.

---
