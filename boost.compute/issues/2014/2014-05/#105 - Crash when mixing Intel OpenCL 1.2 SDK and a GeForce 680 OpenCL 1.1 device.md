# #105 - Crash when mixing Intel OpenCL 1.2 SDK and a GeForce 680 OpenCL 1.1 device. [Closed]

> Username: Eoinocal  
> Created at: 2014-05-06 20:45:56 UTC  
> Updated at: 2014-05-07 04:54:49 UTC  
> Closed at: 2014-05-07 04:54:49 UTC  
> Url: https://github.com/boostorg/compute/issues/105  

When building and linking against Intel's OpenCL SDK on a Haswell i7, which supports OpenCL 1.2, the Hello World and other samples crash while enumerating a GeForce 680 device.  
  
The crash seems to occur at `device.hpp`, `line 65` when the version 1.2 function `clRetain` is called which isn't supported by the Nvidia drivers.  
  
In hindsight it is unsurprising this happens, but it took me a while to figure it out. Is there a way to perform a runtime check and issue a warning if the devices present don't support the OpenCL version the application was compiled with?

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-06 22:19:49 UTC  
> Url: https://github.com/boostorg/compute/issues/105#issuecomment-42367292  

I was just working on a fix for this last night :-). I've implemented a `is_subdevice()` check and only call `clRetainDevice()` if the device is actually a sub-device (which can only happen in OpenCL v1.2 and later). The branch is here (https://github.com/kylelutz/compute/tree/sub-device) and the commit is here (54c2ab3685538cf560db38c2667995566549fa49). It should be merged in later tonight.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-05-07 04:54:49 UTC  
> Url: https://github.com/boostorg/compute/issues/105#issuecomment-42389459  

Fixed in 88b6a8b3d4863589d2730e3dd1eed3103829ec0f.
