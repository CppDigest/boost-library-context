# #479 - Error trying to include <boost/config.hpp> in round_fwd.hpp [Closed]

> Username: vinay0000  
> Created at: 2021-01-03 23:45:46 UTC  
> Updated at: 2021-01-04 09:19:17 UTC  
> Closed at: 2021-01-04 09:19:17 UTC  
> Url: https://github.com/boostorg/math/issues/479  

https://github.com/boostorg/math/blob/b0045c1f43ce5ff0e824d34a4ef7c35c84426d3c/include/boost/math/special_functions/detail/round_fwd.hpp#L11  
  
Looks like a bug? There is no config.hpp in the `boost/` path (https://github.com/boostorg/math/tree/develop/include/boost)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-01-04 09:19:17 UTC  
> Url: https://github.com/boostorg/math/issues/479#issuecomment-753859167  

You need a full Boost install from www.boost.org to use Boost.Math, it isn't (yet) usable standalone.
