# #51 - Upgrade to 1.85 causes arg1_type to be missing [Closed]

> Username: martin5233  
> Created at: 2024-06-05 06:34:20 UTC  
> Updated at: 2024-06-05 14:16:40 UTC  
> Closed at: 2024-06-05 14:16:40 UTC  
> Url: https://github.com/boostorg/function/issues/51  

When compiling the following snippet with older Boost versions, everything is OK:  
  
```  
#include <boost/function.hpp>  
  
using ArgType = typename boost::function<int(int)>::arg1_type;  
```  
  
However with Boost 1.85 it yields an error   
```  
/scratch/apel/new_arch/develop/src/ooa/main_support/test.cpp:7:53: error: ‘arg1_type’ in ‘class boost::function<int(int)>’ does not name a type  
    7 | using ArgType = typename boost::function<int(int)>::arg1_type;  
```  
  
The same applies for the other arguments, if the prototype has multiple arguments. From my understanding this is a regression.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-06-05 13:27:32 UTC  
> Url: https://github.com/boostorg/function/issues/51#issuecomment-2149923445  

You're right, this is a regression. I'll fix it.
