# #262 Add describe dependency [Merged]

> Username: denzor200  
> Created at: 2022-11-29 09:33:06 UTC  
> Updated at: 2022-12-21 05:49:11 UTC  
> Merged at: 2022-12-21 05:49:11 UTC  
> Closed at: 2022-12-21 05:49:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/262  

Just to fix Appveyor-side compilation error:  
```  
.\boost/container_hash/is_described_class.hpp(10,10): fatal error: 'boost/describe/bases.hpp' file not found  
#include <boost/describe/bases.hpp>  
         ^~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```

---
