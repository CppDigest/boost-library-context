# #273 - Boost includes <mpreal.h> that it doesn't install and isn't available in the system [Closed]

> Username: yurivict  
> Created at: 2019-11-04 15:50:13 UTC  
> Updated at: 2019-11-21 17:56:04 UTC  
> Closed at: 2019-11-21 17:56:04 UTC  
> Url: https://github.com/boostorg/math/issues/273  

The FreeBSD package boost-libs-1.71.0_1 installs the file ```/usr/local/include/boost/math/bindings/mpreal.hpp``` that contains:  
```  
#include <mpreal.h>  
```  
that is not installed by boost and isn't available in the base system or through its dependencies.

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-11-19 17:53:53 UTC  
> Url: https://github.com/boostorg/math/issues/273#issuecomment-555628588  

@yurivict : Could you reopen this in boost.math? BTW I believe this is expected.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-11-21 17:56:04 UTC  
> Url: https://github.com/boostorg/math/issues/273#issuecomment-557200885  

Right, the header is there as a bit of "glue" to allow the extended precision library mpreal to interoperate with Boost.Math.  Nothing in Boost.Math will include this header,  and if you're not using mpreal then you won't ever include it in user code either.
