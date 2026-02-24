# #363 - rtree: order of includes matters [Closed]

> Username: daniel-j-h  
> Created at: 2016-09-09 18:08:45 UTC  
> Updated at: 2017-04-04 14:49:41 UTC  
> Closed at: 2016-12-27 15:39:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/363  

I'm currently testing the new Boost 1.62 Beta1 release. Including the rtree header before the parameters header works fine. For the other way around I get errors; here's the first one:  
  
```  
include/boost/geometry/index/parameters.hpp:76:26: error: expected identifier before ‘(’ token  
```  
  
Small self-contained test case:  
  
``` cpp  
#include <boost/geometry/index/parameters.hpp>  
#include <boost/geometry/index/rtree.hpp>  
  
int main() {}  
```  
  
Found this since I'm using `clang-format` and recent versions re-order includes alphabetically.

---

## Comment 1

> Username: awulkiew  
> Created at: 2016-12-27 15:35:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/363#issuecomment-269341350  

Thanks!  
Fix: https://github.com/boostorg/geometry/commit/b8b1cc5c6b084671aa8d1bfbedd4a4668b56acd4
