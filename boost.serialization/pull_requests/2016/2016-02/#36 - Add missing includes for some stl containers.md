# #36 Add missing includes for some stl containers [Closed]

> Username: kartikmohta  
> Created at: 2016-02-10 08:28:01 UTC  
> Updated at: 2016-04-29 03:42:11 UTC  
> Closed at: 2016-04-29 03:42:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/36  

Just including the corresponding header for the stl container should work, but leads to a build failure for some of the containers due to missing includes.

---

## Comment 1

> Username: kartikmohta  
> Created_at: 2016-02-10 08:34:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/36#issuecomment-182252204  

Without these includes, trivial programs such as  
  
``` cpp  
#include "boost/serialization/queue.hpp"  
  
int main(void)  
{  
  return 0;  
}  
```  
  
fail to build.

---

## Comment 2

> Username: kartikmohta  
> Created_at: 2016-04-29 03:42:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/36#issuecomment-215619697  

Looks like these changes have been made in 42b5b6859b757cc0ce3a3e2bef37cc13dea925fc and 8f7b3be1d2104cdc6b5778653cf98ca4dc06a1e6, so closing.

---
