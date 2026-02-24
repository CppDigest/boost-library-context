# #33 Fixed incorrect constant in Dormand-Prince Runge-Kutta Butcher tableau. [Closed]

> Username: thecount2a  
> Created at: 2019-02-22 22:15:22 UTC  
> Updated at: 2019-03-02 19:56:40 UTC  
> Closed at: 2019-03-02 19:56:40 UTC  
> Url: https://github.com/boostorg/odeint/pull/33  

This constant somehow has the incorrect sign when compared to the actual Dormand-Prince Runge-Kutta butcher tableau. This causes the ODE solver to be a bit misguided as it tries to guess steps and estimate error and in the end, while it still works, it does not work as efficiently as it ought to. The entire butcher tableau was created in an internally balanced way but this coefficient being incorrect means that that the boost implementation is much less effective at solving correctly, when compared to another solver with correct coefficients, for the same solver tolerances.  
  
Here are a few references showing that the 1/40 coefficient ought to be positive (look at the lower-right corner of the table):  
  
https://en.wikipedia.org/wiki/Dormand%E2%80%93Prince_method  
  
Here is a paper that clearly has this coefficient as positive on page 1 equation 9:  
  
http://depa.fquim.unam.mx/amyd/archivero/DormandPrince_19856.pdf  
  
Here is a link to the original Dormand Prince paper published in 1980 which I was able to find freely available on sciencedirect.com (Page 5 of the PDF, table 2, again in the lower-right corner of the table):  
  
https://www.sciencedirect.com/science/article/pii/0771050X80900133?via%3Dihub  
  
I hope these references are sufficient to show that the coefficient is incorrect as it currently stands. We have used this ODE solver and found the solving to work much better with the coefficient corrected. This library has been very useful for us, thank you for all of the development work!

---

## Comment 1

> Username: mariomulansky  
> Created_at: 2019-02-23 00:03:19 UTC  
> Url: https://github.com/boostorg/odeint/pull/33#issuecomment-466590185  

Thanks a lot for spotting this error and providing the fix. Could you open a PR against this repo instead: https://github.com/headmyshoulder/odeint-v2  
We will merge it back here, but the other one is our main dev repo with proper CI hooked up.  
Thanks again!

---
