# #1264 - daubechies_scaling coverage. [Open]

> Username: jzmaddock  
> Created at: 2025-05-14 10:20:00 UTC  
> Updated at: 2025-05-14 14:30:55 UTC  
> Url: https://github.com/boostorg/math/issues/1264  

This one really needs @NAThompson 's help.  
  
Any ideas on obtaining meaningful tests for:  
  
https://github.com/boostorg/math/blob/9f03f2985c0db963050be4d968cee4389ee745d3/include/boost/math/special_functions/daubechies_scaling.hpp#L366  
  
and  
  
https://github.com/boostorg/math/blob/9f03f2985c0db963050be4d968cee4389ee745d3/include/boost/math/special_functions/daubechies_scaling.hpp#L473  
  
In daubechies_scaling.hpp?  
  
And then similarly:  
  
https://github.com/boostorg/math/blob/9f03f2985c0db963050be4d968cee4389ee745d3/include/boost/math/special_functions/daubechies_wavelet.hpp#L123  
  
In daubechies_scaling.hpp.

---

## Comment 1

> Username: NAThompson  
> Created at: 2025-05-14 14:24:44 UTC  
> Updated at: 2025-05-14 14:30:54 UTC  
> Url: https://github.com/boostorg/math/issues/1264#issuecomment-2880450506  

@jzmaddock : Yes, unit tests for the derivative can be obtained just by doing finite differencing with the optimal spacing: https://github.com/boostorg/math/blob/develop/include/boost/math/differentiation/finite_difference.hpp  
  
That being said, the accuracy really can degrade for _both_ methods and picking a tolerance will be quite arbitrary. We can certainly demonstrate accuracy "in the eyeball norm", but I doubt in any other.  
  
As to the arrays, I'm not convinced those can really be covered. They were selected rather arbitrarily by the conditions that 1) they didn't explode the memory use, and 2) they achieved a good accuracy under the constraint of 1).  
  
My apologies-my brain is DDOS's until at least June!
