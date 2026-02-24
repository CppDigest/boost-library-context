# #141 - Support BOOST_NO_ANSI_APIS [Closed]

> Username: zhaohuaxishi  
> Created at: 2021-04-08 15:04:59 UTC  
> Updated at: 2021-04-09 08:40:28 UTC  
> Closed at: 2021-04-09 08:40:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/141  

```  
#define BOOST_NO_ANSI_APIS 1  
#include "boost/interprocess/sync/file_lock.hpp"  
int main() {}  
```  
Fails to compile  
  
See also https://github.com/boostorg/process/issues/26

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-04-09 08:00:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/141#issuecomment-816496518  

¿Why should we support this in Interprocess? ¿Why is useful?

---

## Comment 2

> Username: zhaohuaxishi  
> Created at: 2021-04-09 08:40:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/141#issuecomment-816523647  

> ¿Why should we support this in Interprocess? ¿Why is useful?  
  
https://www.boost.org/doc/libs/1_75_0/libs/winapi/doc/html/winapi/config.html  
  
>   
  
BOOST_NO_ANSI_APIS | This macro is defined by Boost.Config on platforms that do not support narrow-character versions of some APIs. Boost.WinAPI will not declare narrow-character APIs when this macro is defined.  
-- | --
