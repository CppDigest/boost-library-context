# #284 - std::copysign in roots.hpp not working under uClibc-ng [Closed]

> Username: neheb  
> Created at: 2019-12-20 01:56:42 UTC  
> Updated at: 2019-12-23 15:25:03 UTC  
> Closed at: 2019-12-23 15:25:03 UTC  
> Url: https://github.com/boostorg/math/issues/284  

https://github.com/boostorg/math/blob/develop/include/boost/math/tools/roots.hpp#L887 fails as uClibc-ng does not have copysign in std.

---

## Comment 1

> Username: neheb  
> Created at: 2019-12-20 02:25:21 UTC  
> Url: https://github.com/boostorg/math/issues/284#issuecomment-567760030  

using boost::math::copysign; seems to work. No idea what impact this has.

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-12-23 15:25:03 UTC  
> Url: https://github.com/boostorg/math/issues/284#issuecomment-568503519  

Thanks for the fix!
