# #261 - Warning unused-local-typedef - Android NDK20 [Closed]

> Username: crazyhappygame  
> Created at: 2019-10-13 19:26:26 UTC  
> Updated at: 2019-10-17 14:33:44 UTC  
> Closed at: 2019-10-17 14:33:44 UTC  
> Url: https://github.com/boostorg/math/issues/261  

```  
In file included from G:/j/A2/external/boost\boost/math/interpolators/vector_barycentric_rational.hpp:16:  
G:/j/A2/external/boost\boost/math/interpolators/detail/vector_barycentric_rational_detail.hpp:45:11: error: unused type alias 'Real' [-Werror,-Wunused-local-typedef]  
using Real = typename TimeContainer::value_type;  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-10-13 20:21:36 UTC  
> Url: https://github.com/boostorg/math/issues/261#issuecomment-541455588  

@crazyhappygame : Fixed in 5e15bc8, thanks!

---

## Comment 2

> Username: crazyhappygame  
> Created at: 2019-10-14 06:44:33 UTC  
> Url: https://github.com/boostorg/math/issues/261#issuecomment-541521221  

@NAThompson please double check your fix. For me it looks like debug/release configuration problem. Real is used in debug and not used in release config

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-10-14 12:53:45 UTC  
> Url: https://github.com/boostorg/math/issues/261#issuecomment-541659894  

@crazyhappygame : Should *actually* be fixed in  fe25853c2716ba397383f0b72a5e0b56c33b101f.
