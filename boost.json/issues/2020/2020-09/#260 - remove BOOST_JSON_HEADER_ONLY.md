# #260 - remove BOOST_JSON_HEADER_ONLY [Closed]

> Username: vinniefalco  
> Created at: 2020-09-03 18:03:21 UTC  
> Updated at: 2020-09-06 22:09:03 UTC  
> Closed at: 2020-09-06 22:09:03 UTC  
> Url: https://github.com/boostorg/json/issues/260  

This complicates development, and all we are doing is saving the user from adding  
```  
#include <boost/json/src.hpp>  
```  
  
in _one_ of their translation units. Kind of a net loss.
