# #34 - Gauss system support [Open]

> Username: micrenda  
> Created at: 2018-11-08 11:20:03 UTC  
> Updated at: 2018-11-13 13:02:38 UTC  
> Url: https://github.com/boostorg/units/issues/34  

Hello  
  
I implemented the support for Gauss units. It is not perfect (it miss the conversions from/to SI and CGS) but I tried to follow the existing design as possible.  
  
Unfortunately, I had to duplicate a set of physical_dimensions (Gauss define electric charge differently): you can find it in physical_dimensions/gs directory  
  
If you want, and you know how to implements the converters,  you can integrate to boost/units release. If you think this implementation does not fit the design of Boost Units please just notice me so I can create a standalone project (with only my modification).  
  
The modifications can be found here (branch: gauss)  
https://gitlab.com/micrenda/boost-units-gauss/tree/gauss  
  
Regards

---

## Comment 1

> Username: micrenda  
> Created at: 2018-11-13 13:02:37 UTC  
> Url: https://github.com/boostorg/units/issues/34#issuecomment-438257984  

I created a standalone version of my implementation.  
  
Just download and put in your include path:  
https://gitlab.com/micrenda/boost-units-gauss-standalone
