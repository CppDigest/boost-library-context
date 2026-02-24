# #357 - Add algorithm tuning infrastructure [Open]

> Username: kylelutz  
> Created at: 2014-12-29 17:50:17 UTC  
> Updated at: 2017-03-21 04:14:52 UTC  
> Url: https://github.com/boostorg/compute/issues/357  

Add infrastructure for tuning algorithm parameters to achieve maximum performance on the hardware present at run-time. Many algorithms have variable execution parameters (e.g. `VPT`, `TPB`, etc.) which can be tuned for the specific compute device to yield better performance and allow the algorithms to scale better to different hardware.  
##

---

## Comment 1

> Username: roshanrags  
> Created at: 2015-01-25 14:50:44 UTC  
> Url: https://github.com/boostorg/compute/issues/357#issuecomment-71375828  

What is the status of this now? Has somebody started working on this?

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-01-25 16:55:40 UTC  
> Url: https://github.com/boostorg/compute/issues/357#issuecomment-71380926  

Yeah, I have an implementation almost ready for this. I'll try to get it pushed sometime this week.

---

## Comment 3

> Username: kylelutz  
> Created at: 2015-03-15 23:22:53 UTC  
> Url: https://github.com/boostorg/compute/issues/357#issuecomment-81291171  

Initial implementation in PR #434.
