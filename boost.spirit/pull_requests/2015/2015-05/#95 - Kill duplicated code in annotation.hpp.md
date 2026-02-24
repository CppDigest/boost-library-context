# #95 Kill duplicated code in annotation.hpp. [Merged]

> Username: mlang  
> Created at: 2015-05-18 00:03:21 UTC  
> Updated at: 2015-05-19 12:57:29 UTC  
> Merged at: 2015-05-18 05:28:38 UTC  
> Closed at: 2015-05-18 05:28:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/95  

There is no need fetch the error_handler and call its tag method twice.

---
