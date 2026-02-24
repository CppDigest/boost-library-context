# #125 X3: Use decltype instead of result_of. [Merged]

> Username: mlang  
> Created at: 2015-06-19 07:50:17 UTC  
> Updated at: 2015-06-19 09:28:29 UTC  
> Merged at: 2015-06-19 09:28:29 UTC  
> Closed at: 2015-06-19 09:28:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/125  

boost/utility/result_of.hpp is rather expensive to include. Using decltype  
here makes the compile time go down by almost half a sec.

---
