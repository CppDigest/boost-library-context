# #17 - how to change the include dir synchronously? [Open]

> Username: clee01  
> Created at: 2023-08-15 07:45:05 UTC  
> Updated at: 2023-08-16 01:33:05 UTC  
> Url: https://github.com/boostorg/bcp/issues/17  

```cpp  
// before bcp (normally use)  
#include <boost/foo/bar.hpp>  
  
boost::foo::bar bar;  
```  
  
```cpp  
// after bcp (ideally use)  
#include <toast/foo/bar.hpp>  
  
toast::foo::bar bar;  
```  
  
* but now i can only use `#include <boost/foo/bar.hpp>` with `toast::foo::bar bar` instead
