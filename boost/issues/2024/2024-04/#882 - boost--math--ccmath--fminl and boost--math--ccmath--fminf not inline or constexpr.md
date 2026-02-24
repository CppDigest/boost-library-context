# #882 - boost::math::ccmath::fminl and boost::math::ccmath::fminf not inline or constexpr [Open]

> Username: rndx21033  
> Created at: 2024-04-06 01:22:45 UTC  
> Updated at: 2025-03-18 20:31:49 UTC  
> Url: https://github.com/boostorg/boost/issues/882  

This results in potential multiple definition of these two functions.  Affects at least 1.83.0, 1.84.0, and the develop branch.  
  
[fmin.patch.txt](https://github.com/boostorg/boost/files/14891890/fmin.patch.txt)

---

## Comment 1

> Username: philmb3487  
> Created at: 2024-04-07 17:18:14 UTC  
> Url: https://github.com/boostorg/boost/issues/882#issuecomment-2041535688  

yes this is still an issue today.

---

## Comment 2

> Username: rtmadduri  
> Created at: 2025-03-18 20:31:48 UTC  
> Url: https://github.com/boostorg/boost/issues/882#issuecomment-2734647097  

It is an issue today also
