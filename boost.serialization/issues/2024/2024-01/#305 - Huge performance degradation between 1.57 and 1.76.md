# #305 - Huge performance degradation between 1.57 and 1.76 [Open]

> Username: vivek0916  
> Created at: 2024-01-04 10:11:02 UTC  
> Updated at: 2024-01-04 19:15:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/305  

Hello,  
  
For the past several years, we have been utilizing Boost, as part of upgradation from an older(1_57) service to a later Boost version(1_76).  
We've incorporated the boost libraries (libboost_serialization-vc142-mt-x64-1_76, libboost_wserialization-vc142-mt-x64-1_76) to facilitate XML serialization. It has been noticed that there is a significant decline in performance when compared to the 1_57 version.   
It would be highly appreciated if you could provide insights on this performance degradation.  
  
Thanks,  
Vivek

---

## Comment 1

> Username: robertramey  
> Created at: 2024-01-04 19:15:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/305#issuecomment-1877630772  

I never get complaints about the performance of the serialization library, so I'd be curious to know more about this.  
  
I'd like to see:  
  
1. more information regarding the environment: compiler, os, library etc.  
2. a test program demonstrating the issue.  
3. results of running a profiler on that test program.  A profiler would reveal the amount of time spent in each function in the program and library.  
  
Robert Ramey
