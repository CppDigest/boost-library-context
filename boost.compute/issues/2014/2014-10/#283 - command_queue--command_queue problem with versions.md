# #283 - command_queue::command_queue problem with versions [Closed]

> Username: WojciechMula  
> Created at: 2014-10-24 08:19:00 UTC  
> Updated at: 2017-03-21 12:16:43 UTC  
> Closed at: 2017-03-21 12:16:43 UTC  
> Url: https://github.com/boostorg/compute/issues/283  

I've intel-based system (output from clinfo.exe):  
  
Number of platforms:                 2  
  Platform Profile:              FULL_PROFILE  
  Platform Version:              OpenCL 1.2   
  Platform Name:                 Intel(R) OpenCL  
  Platform Vendor:               Intel(R) Corporation  
  Platform Profile:              FULL_PROFILE  
  Platform Version:              OpenCL 2.0   
  Platform Name:                 Experimental OpenCL 2.0 CPU Only Platform  
  Platform Vendor:               Intel(R) Corporation  
  
As we see there are two devices, supporting different OpenCL versions, however headers shipped with the newest SDK from Intel defines CL_VERSION_2_0. This is the problem, because command_queue::command_queue selects an initialization code using defines, and if it is called on device supporting version 1.2 then strange errors happen in a drivers. When I manually edited library code to use initialization for 1.2 everyting works ok.  
  
Maybe API version should be obtained from a device info? Or is it a problem in vendor SDK?  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-10-25 15:47:16 UTC  
> Url: https://github.com/boostorg/compute/issues/283#issuecomment-60486931  

Yeah, currently the OpenCL API functions used by Boost.Compute are determined at compile-time based on the `CL_VERSION_X_Y` macros. For now the best way is to compile against one set of headers and use the corresponding library (or other compatible library). I'll think about better ways to support this.

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-03-21 12:16:43 UTC  
> Url: https://github.com/boostorg/compute/issues/283#issuecomment-288060974  

It should be fixed by https://github.com/boostorg/compute/commit/60f4b31bdd374e088eb26262c9b7970a08f9ab8c.
