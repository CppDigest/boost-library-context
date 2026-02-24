# #16 Add missing header to right_open_interval.hpp [Closed]

> Username: Timmmm  
> Created at: 2018-08-30 14:36:07 UTC  
> Updated at: 2019-05-13 14:28:04 UTC  
> Closed at: 2019-05-13 14:28:04 UTC  
> Url: https://github.com/boostorg/icl/pull/16  

Without this `#include` if you just do  
  
    #include <boost/icl/right_open_interval.hpp>  
  
you'll get a load of errors about `BOOST_CONCEPT_ASSERT` not being an expression, and various other things. This fixes that.

---
