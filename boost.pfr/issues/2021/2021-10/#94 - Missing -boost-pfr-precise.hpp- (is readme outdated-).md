# #94 - Missing "boost/pfr/precise.hpp" (is readme outdated?) [Closed]

> Username: myxo  
> Created at: 2021-10-04 18:04:22 UTC  
> Updated at: 2021-10-09 08:09:20 UTC  
> Closed at: 2021-10-09 08:08:22 UTC  
> Url: https://github.com/boostorg/pfr/issues/94  

Hi.  
  
README.md has code that  
```c++  
#include "boost/pfr/precise.hpp"  
```  
but it missing in repository. It seems that README.md should be updated.  
  
ps. I actually don't know much about pfr, I stumbled on this issue while compiling 3d party library. What header should I replace it with? Just `boost/pfr.hpp`?

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-10-09 08:09:19 UTC  
> Url: https://github.com/boostorg/pfr/issues/94#issuecomment-939253131  

Many thanks for the bug report!  
  
Fixed.
