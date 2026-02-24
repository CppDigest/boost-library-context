# #113 - Deprecation warning with boost 1.73.0 in copy.hpp [Closed]

> Username: rogerorr  
> Created at: 2020-04-30 13:59:48 UTC  
> Updated at: 2020-04-30 14:36:41 UTC  
> Closed at: 2020-04-30 14:36:41 UTC  
> Url: https://github.com/boostorg/iostreams/issues/113  

`iostreams/copy.hpp` includes `<boost/bind.hpp>` which causes a deprecation warning with boost 1.73.0 by simply #including this file.  
  
```  
..\boost\boost/bind.hpp(41): note: The practice of declaring the Bind placeholders (_1, _2, ...)  
in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using  
namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to  
retain the current behavior.  
```  
  
As far as I can see iostreams itself does not use the placeholders, so replacing the include with   
`#include <boost/bind/bind.hpp>` will resolve the problem here; but of course with the risk of causing a downstream problem for anyone accidentally relying on `_1` etc. at global scope.

---

## Comment 1

> Username: mclow  
> Created at: 2020-04-30 14:36:41 UTC  
> Url: https://github.com/boostorg/iostreams/issues/113#issuecomment-621894228  

Since Boost.iostreams did not use it, I removed the include in 87d113c.
