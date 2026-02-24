# #194 - Add nvcc to test matrix [Closed]

> Username: NAThompson  
> Created at: 2019-04-04 13:14:07 UTC  
> Updated at: 2019-08-03 21:19:53 UTC  
> Closed at: 2019-08-03 21:19:53 UTC  
> Url: https://github.com/boostorg/math/issues/194  

The goal here is to make sure that no host code is broken when using nvcc; clearly at this point we have no device code. (Actually, is this an feature request for boost.config?)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-04-04 16:56:27 UTC  
> Url: https://github.com/boostorg/math/issues/194#issuecomment-479979231  

Probably for config first yes.  
  
This is harder than it looks too as neither appveyor, nor travis have nvcc installed, and neither can run device code.  
Also be aware that compiling a .cpp file with nvcc is sort of pointless as it just forwards to the host compiler, it's only when you build a .cu file that the nvcc compiler itself does any work.
