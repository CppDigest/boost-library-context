# #515 - daubechies scaling test throwing bad_alloc [Closed]

> Username: jzmaddock  
> Created at: 2021-02-02 18:42:28 UTC  
> Updated at: 2021-02-07 08:21:50 UTC  
> Closed at: 2021-02-07 08:21:50 UTC  
> Url: https://github.com/boostorg/math/issues/515  

See here: https://drone.cpp.al/boostorg/math/10/85/2  
  
@NAThompson : Is there any reason not to just catch the exception and exit?  The test crunches through about 2GB of memory which may well run the VM out of memory.  
  
I can add the obvious patch to this PR if you're happy with that.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-02-02 18:46:29 UTC  
> Url: https://github.com/boostorg/math/issues/515#issuecomment-771882867  

daubechies_wavelet_test has a similar error though this one just says "killed" :(  
  
https://drone.cpp.al/boostorg/math/10/94/2

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-02-02 20:22:21 UTC  
> Url: https://github.com/boostorg/math/issues/515#issuecomment-771954275  

@jzmaddock : Thanks for taking care of this; there is no reason to not catch the bad_alloc as you say.
